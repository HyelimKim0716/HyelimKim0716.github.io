RecyclerView 최하단에 여백을 추가하기 위해 RecyclerView 에 padding 을 추가하고 clipToPadding 속성을 true 로 정의하는 경우,
layoutManager.findLastCompletelyVisibleItemPosition() 의 return 값이 기대했던 바와 다르다는 것을 알게 되었다.
화면에서는 0 ~ 6 까지 position 에 해당하는 item view 가 보여지나,
layoutManager.findLastCompletelyVisibleItemPosition() 에서 padding 을 지정한만큼을 제외하고 5 를 return 한다.

따라서 ItemDecoration 을 이용하여 최하단 position 에 도달하였을 경우, padding 만큼 추가하였으나, 어떤 경우 위와 같은 문제가 동일하게 발생한다. 로드된 모든 item view 가 screen 에 딱 맞게 한 번에 보여질 때 발생하는데, 예를 들면 (ItemDecoration 의 마지막 아이템에 74dp 영역이 추가되었을 경우) 단말의 screen height 가 최대 다섯개의 아이템뷰가 보여질 수 있고, 로드된 모든 아이템도 다섯개 뿐일 때, findLastCompletelyVisibleItemPosition 이 4가 아닌 3을 return 한다.

예상컨데 하단에 추가한 padding 이 마지막 itemView 에 포함되는 것이 아닐까 싶다. 최대 다섯개의 아이템뷰가 보여질 수 있고, 로드될 수 있는 모든 아이템이 2개일 경우는 findLastCompletelyVisibleItemPosition 이 1 을 정상적으로 return 한다.