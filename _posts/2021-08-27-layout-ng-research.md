---
title: LayoutNG 기본
categories:
- Blink
feature_image: "https://picsum.photos/2560/600?image=872"
---

# LayoutNG

- 레이아웃은 화면에서 DOM 요소의 크기와 위치를 결정하는 immutable 프래그먼트 트리를 만드는 과정이다.
- 조금 더 구체적으로 말하면, NGLayoutAlgorithm을 이용해 NGLayoutResult를 만들어내고, 이 NGLayoutResult를 통해 페인트 단계에서 어느 위치에 어떤 크기로 그릴지 결정한다. 
- 다시 말해, input을 NGLayoutAlgorithm에 넣고 output을 생성하는 것이 LayoutNG이다. 

```
LayoutAlgorithm 코드 분석
   - input : NGLayoutParams
    - 레이아웃을 수행해야하는 node
    - 레이아웃 결과물을 담을 수 있는 객체들
     (ConstraintsSpace, FragmentGeometry(border, padding, scroll), break token 생성 여부 등)
   - output : NGLayoutResult
    - 실제 fragments (NGPhysicalFragments)
    - 계산된 box 사이즈 (intrinsic_block_size_)
    - Dirty check에 사용되는 차지하는 영역 정보 
     (NGConstraintsSpace: wrap mode를 위한 exclusion space, out of flow 여부 등의 정보를 포함)
```

# Input of NGLayoutAlgorithm

## 1. NGConstraintSpace

- Parent로 부터의 모든 layout information을 포함한다.(e.g. available_size_, exclusion_space_, bfc_offset)
- 쉽게 말해, NG에서 도입된 layout 가능한 공간

```
NGExclusionSpace

- NG에서 도입된 제외구역으로 이구역에는 자식 레이아웃이 들어갈수가 없다.
- 여기서 Derived 된 ConstraintSpace를 Layout Oppertunity라고 하고 자식에게 전달하는 용도로 쓰임
```
## 2. NGLayoutInputNode

- NGLayoutAlgorithm을 수행하기 위한 layoutObject의 wrapper 노드이다. 
- 현재 type이 block과 inline 만 존재한다. 그래서 NGBlockNode와 NGInlineNode가 NGLayoutInputNode를 상속한다.

## 3. NGBreakToken

- BreakToken은 레이아웃을 위한 연속 토큰이다. BreakToken이 nullptr이면 더 이상 layout 할게 없다는 뜻이다.

# Output of NGLayoutAlgorithm

## 1. NGLayoutResult

- NGLayoutAlgorithm의 결과 데이터를 저장하는 곳이다. 
- NGPhysicalFragment 형태의 지오메트리 정보와 레이아웃 중에만 필요하고 이 개체에 저장되는 추가 데이터가 포함된다. 
 ```
하나의 LayoutObject가 여러 PhysicalFragment를 만들더라도 NGLayoutResult는 `시작 NGPhysicalFragment`만 들고 있음
시작부를 기점으로 NGLink로 연결되어 있음
```
- 멤버 변수로 physical_fragment_, bfc_offset_, space_, exclusion_space 등이 있다.


`NGPhysicalFragment`

```
- 멤버 변수로 해당 layoutObject, PhysicalSize size_, break_token_ 등이 있다.
- Fragment에서 position 정보를 들고 있지 않다. 부모 fragment로부터 상대적인 위치는 NGLink에서 저장하고 있다.
- NGPhysicalFragment->Children()을 호출해 NGLink 객체를 얻을 수 있다.
```

`NGLink`
```
- 멤버 변수로 해당 fragment, offset을 들고 있다. 
- Fragment 자체에는 위치 정보가 없으므로 배치에 관계없이 전체 프래그먼트 하위 트리를 재사용하고 캐시할 수 있다.
- 예를 들어, 2개의 ColumnBox를 child로 갖고 있는 NGPhysicalBoxFragment가 있고, 해당 Box의 Children()을 호출하면 2개의 NGLink 배열이 있다.
- 각 NGLink는 해당 fragment와 offset 정보를 들고 있다. 
```

`NGPhysicalBoxFragment`
- 즉 NGLink children_[]을 들고 있음
- ex) 하나의 layout object에서 3 페이지를 만들면 
- 1. ng_block_node(pages)는 ng_page_layout_algorithm을 통해 NGLayoutResult에 NGPhysicalBoxFragment가 담김
- 2. NGPhysicalBoxFragment는 내부적으로 children_(NGLink)로 이어져 있어서 총 3개의 자식으로 연결되어 있음

```
class CORE_EXPORT NGPhysicalBoxFragment final : public NGPhysicalFragment {
 public:
  	static scoped_refptr<const NGPhysicalBoxFragment> Create(
 	 NGBoxFragmentBuilder* builder,
 	 WritingMode block_or_line_writing_mode);
		... 	
  	LayoutUnit baseline_;
  	LayoutUnit last_baseline_;
 	 NGInkOverflow ink_overflow_;
  	NGLink children_[];
```