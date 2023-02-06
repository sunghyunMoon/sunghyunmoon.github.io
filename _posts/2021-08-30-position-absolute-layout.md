---
title: position aboslute의 blink layout 분석
categories:
- Blink
feature_image: "https://picsum.photos/2560/600?image=872"
---

CSS 스타일로 position을 absolute로 설정하고 offset 설정할 때 이 offset은 과연 어디서 설정해줄까?라는 궁금증으로 Blink Rendering Engine을 분석해봤다. 

#### Position absolute의 top 오프셋과 BlockLayoutAlgorithm의 관점
- 예를 들어, top offset 값을 100px로 준다면, 이 100px은 과연 어디서 오프셋을 설정해줄까??
- BlockLayoutAlgorithm의 FinishLayout에서 NGOutofFlowLayoutPart의 Run 수행
- Run 수행 중 자식들 중 position이 absolute인(outofflow 특성을 가진) 자식들이 있으면 candidates에 등록되어 있음
- Candidates를 가지고 NGOutOfFlowLayoutPart::LayoutCandidates 수행
    - Loop을 돌며 자식들에 대해 아래 로직 수행
    - CalculateOffset에서 top, left, right, bottom을 계산해 offset_info에 저장
    - NGOutOfFlowLayoutPart::LayoutOOFNode => NGOutOfFlowLayoutPart::Layout
        - GenerateFragment를 수행하면서 position이 absolute인 자식들의 BlockLayoutAlogorithm을 수행함
        - GenerateFragment의 반환 값인 layout_result에 대해 CalculateOffset에서 계산한 오프셋 값(top, left, right, bottom) 값들을 넣어줌
            - 코드 : layout_result->GetMutableForOutOfFlow().SetOutOfFlowPositionedOffset
- 정리하면, BlockLayoutAlgorithm을 수행하면서 오프셋을 정해주는 줄 알았는데, BlockLayoutAlgorithm 완료 후 layout_result에 대해 Offset 정보를 설정해줌!

#### blink layout call

- contentEditable이 true인 element에서 에디팅을을 하면 Blink에서는 과연 layout이 어떻게 돌아갈까?
- 디버깅을 해보면...
- 에디팅을 하면 layoutObject의 dirty_bit가 true로 설정되고, 부모를 타고 올라가면서 부모마다 dirty_bit_을 true로 설정함(정확히는, bitfields_.SetNormalChildNeedsLayout 를 true로 셋팅)
- 최상단인 layoutView까지 올라간 후, 자식들을 순회하며 layout 콜이 들어감
- dirty_bit가 true인 경우는 레이아웃 알고리즘을 수행하고, dirty_bit가 clear한 경우는 알고리즘을 수행하지 않고 캐쉬해놓은 layout_result를 그대로 사용하고, 자식들에 대해서 layout call이 가지 않음
- 그래서 엔터를 치는 경우는 단순히 밑으로 밀려나는 blocknode 들에대해서는 layoutcall이 가지만, blocklayout 알고리즘을 수행하지 않고 캐쉬해놓은 layout_result를 그대로 사용한다.
- 정리하면, 엔터를 친다고해서 밑으로 밀려나는 blocknode들에 대해 모두 BlockLayoutAlgorithm을 수행해서 새로운 fragement를 만들어내지 않고, dirty_bit을 이용해 필요한 blocknode에 대해서만 layoutAlgorithm을 수행한다!

<h3>끝!</h3>
