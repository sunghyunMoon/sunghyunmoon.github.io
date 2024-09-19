---
title: 실무에서 Typescript Mixin 적용해보기
categories:
- Typescript
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/typescript.png"
---

이번에 오피스에 댓글 기능 공통 프레임워크를 개발을 하면서, 타입스크립트 Mixin을 사용해봤고, Mixin을 실무에 적용해보면서 공부했던 것들을 한번 정리해보고자 한다.

### Mixin이 필요했던 상황

- 문서에서 본문 content내의 객체와 content와 별개인 댓글 객체와 연결을 시키려면, 기존엔 content 내 특정 객체에서 댓글 객체에 대한 Id 리스트(commentList)를 들고 있었다. 
- 하지만, 특정 모듈에만 있는 댓글 기능을 전 모듈로 올리는 공통 프레임워크 작업을 진행하면서, 댓글을 삽입할 수 있는 객체들이 많아졌고, **댓글이 삽입 가능한 모든 클래스들에 대해 commentList 프로퍼티와 그것을 관리하는 메서드 들이 중복으로 필요한 상황**이 생겼다.
- 또한, **기존의 클래스들은 이미 공통 Node 속성들을 갖고 있는 Base 클래스들을 통해 상속받고 있는 상황**이었다.
- **따라서, 다중 상속을 받는 것과 유사한 효과를 얻을 수 있고, 중복 코드없이 하나의 코드로 코드의 재사용성을 증가시키기 위해 떠오르는 것이 바로 Typescript Mixin이었다.**

### Mixin이란?

- **타입스크립트에서 Mixin은 여러 클래스 간에 공통된 기능을 재사용할 수 있도록 하는 디자인 패턴**이다. Mixin은 클래스를 정의하는 대신 함수 형태로 구현되며, 이 함수는 다른 클래스에 특정 기능을 추가하거나 확장할 때 사용된다. Mixin은 다중 상속을 지원하지 않는 자바스크립트의 특성상, 클래스를 여러 소스로부터 기능을 결합하거나 공유하는 방법으로 활용된다.
- 조금 더 구체적으로 얘기해보자. 클래스 A가 클래스 B를 확장 해서 그 기능을 받는 것이 아니라 **함수 B가 클래스 A 를 받고 기능이 추가된 새 클래스를 반환하는 것이다. 함수 B가 믹스인**이다.
- 따라서 믹스인은 아래와 같은 함수다.
    - 1) 생성자(constructor)를 받음
    - 2) 생성자를 확장하여 새 기능을 추가한 클래스 생성
    - 3) 새 클래스 반환
    
### 실무 Mixin 코드

- 아래는 완전히 동일하지는 않지만, 실무에 적용한 Mixin 코드다.

```js
type CommentConstructor<T extends object = object> = abstract new (...args: any[]) => T;

/**
 * CommentMixin이 결합된 클래스의 인스턴스 타입 정의
 */
export type CommentableDocumentNode = Commentable & DocumentNode;

export function CommentMixin<NodeBase extends CommentConstructor<DocumentNode>>(
  DocumentNodeBase: NodeBase
) {
  abstract class CommentableNode extends DocumentNodeBase implements Commentable {
    /**
     * CommentableDocumentNode의 commentList를 deserialize
     */
    @serializable(
      alias(
        'elementType',
        custom(
          () => SKIP,
          (value, context) => {
            const data = context.json;
            const { commentList } = data;
            if (commentList !== undefined) {
              const parsedCommentList = JSON.parse(commentList);
              context.target.setCommentList(parsedCommentList);
              return parsedCommentList;
            }
            return [];
          }
        )
      )
    )
    /**
     * CommentableDocumentNode에서 관리하는 commentList
     */
    @observable.shallow
    private commentList: Array<number> = [];

    // eslint-disable-next-line @typescript-eslint/no-explicit-any
    constructor(...args: any[]) {
      super(...args);
      makeObservable(this);
    }

    public setCommentList(commentList: Array<number>): void {
      this.commentList = commentList;
    }

    public getCommentList(): Array<number> {
      return this.commentList;
    }

    public addComment(commentId: number): void {
      this.commentList.push(commentId);
    }

    public removeComment(commentId: number): void {
      this.commentList = this.commentList.filter(curId => curId !== commentId);
    }

    public hasComment(): boolean {
      return this.commentList.length > 0;
    }

    public isCommentMixin(): boolean {
      return true;
    }
  }

  return CommentableNode;
}

/**
 * CommentableDocumentNode에 대한 타입 가드
 */
// eslint-disable-next-line @typescript-eslint/no-explicit-any
export const isCommentable = (obj: OfficeTreeNodeTypes | any): obj is CommentableDocumentNode =>
  typeof obj === 'object' &&
  obj !== null &&
  typeof obj.isCommentMixin === 'function' &&
  obj.isCommentMixin();
```

- 먼저 생성자 타입 코드를 보자.

```js
type CommentConstructor<T extends object = object> = abstract new (...args: any[]) => T; 
```

- CommentContsructor라는 생성자 타입을 정했다. 생성자 타입의 반환값이 제네릭으로 되어있고, 이 제네릭은 object 타입으로 제한했고, 기본값으로도 object 타입을 설정했다.
- 인자는 가변 인자를 받을 수 있고 타입은 any[]로 정의된다. 즉, 인수의 개수나 타입에 상관없이 어떤 인자로도 허용된다.
- 그리고 생성자 합수의 반환 타입을 T다. 이 생성자를 사용해 인스턴스화한 객체의 타입이 T로 설정된다.
- 위 코드처럼 생성자 타입의 인자를 ...args: any[]로 받는 것은 어떠한 객체나 인수도 허용하도록하는 일반적인 방식이다. 하지만, 특정 구조나 속성을 가진 객체만 받을 수 있도록 제약을 둘 수 도 있다. 
    - 예를 들어, x: number와 y: string 같은 특정 인수를 요구하는 생성자 타입을 정의하려면, 아래와 같이 생성자 타입을 정의할 수 있다.

```js
type Constructor = new (x: number, y: string) => any;
```

- 따라서, 위와 같이 생성자 타입을 정의하면, Mixin이 받는 기본 클래스의 생성자 타입을 명확히 지정할 수 있다. 이것은  타입스크립트가 코드의 타입 체크를 더욱 정확하게 할 수 있도록 도와준다.
- 정리하면, Mixin 함수의 타입을 명확하게 정의함으로써, 이 함수를 사용할 때 필요한 인수와 반환 타입을 명확히 할 수 있다. 당연히 코드의 가독성와 유지보수성에 좋은 영향을 미친다.

```js
export function CommentMixin<NodeBase extends CommentConstructor<DocumentNode>>(
  DocumentNodeBase: NodeBase
) {
  abstract class CommentableNode extends DocumentNodeBase implements Commentable {
      ...
      return CommentableNode;
  }
}
```

- 위 코드를 통해 Mixin 패턴을 사용하여 클래스를 확장한다. CommentMixin이라는 함수가 특정 클래스에 commentList 관리 기능을 추가하는 구조다.
- CommentMixin 적용이 필요한 모든 클래스가 DocumentNode라는 클래스를 상속 받고 있고, CommentMixn을 통해 확장된 클래스에서 기존 DocumentNode 클래스의 프로퍼티와 메서드를 사용하기 위해서 CommentMixin 내부 클래스도 DocumentNode를 상속 받도록 했다.
- 이 CommentMixin 함수에 인자의 타입은 제네릭 타입의 NodeBase이고, 이 NodeBase는 CommentConstructor\<DocumentNode> 타입을 상속하는 타입이어야 한다. 즉, **CommentMixin 함수의 인자는 DocumentNode라는 객체를 생성할 수 있는 클래스 또는 생성자여야 한다는 뜻**이다.
- 그리고, CommentableNode라는 새로운 추상 클래스를 선언했고, CommentableNode 클래스는 인자로 들어온 DocumentNodeBase 클래스를 상속 받는다.
- 즉 위코드를 정리하면, **CommentMixin 함수는 DocumentNode 기본 클래스를 확장하여, Commentable이라는 interface로 구현하는 추상 클래스 CommentableNode를 반환하는 것을 의미**한다.
- 그리고 isCommentable이라는 타입가드를 만들어, 정적으로 타입을 좁히고 commentList 관리 메서드를 사용할 수 있도록 했다. 

### Mixin 장점

- 댓글이 삽입 가능한 클래스들에서 commentList를 관리해야하는 **중복 코드를 줄일 수 있다.**
- 그리고, **commentList를 관리하는 기능을 독립적인 단위로 분리해 모듈화했기 때문에 코드 가독성 및 유지보수에 용이하다.**
- 그리고 댓글 객체에서도 맵핑되어 있는 객체를 프로퍼티로 들고 있는데, 이 맵핑 객체의 타입이 다양할 수 있다. 예를 들어, 텍스트, 도형, 그림, 테이블 등등이 있을 수 있다. 그러면 댓글 삽입 가능한 클래스를 추가할때 마다 유니온 타입으로 추가하거나 혹은 맵핑 객체가 특정 모듈에만 있다면 댓글 클래스 자체를 상속받아서 별도로 구현할 수도 있다.
- CommentMixin을 통해 commentList 기능이 확장된 클래스들에 대해 위와 같이 CommentableDocumentNode라고 타입을 정하였기 때문에, 댓글 객체에서는 별도의 타입 추가나 상속 구현 없이, 맵핑된 객체의 프로퍼티 타입으로 CommentableDocumentNode라고만 정해주면 된다.

<h3>끝!</h3>
