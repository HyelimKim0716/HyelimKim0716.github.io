### android:nestedScrollingEnabled
RecyclerView 내에 RecyclerView 가 존재할 경우, '안' 의 리스트를 스크롤할 때 '부모' 의 리스트 스크롤 여부를 판단하는 옵션.
<br>
'자식' RecyclerView 에서 `android:nestedScrollingEnabled="false"` 를 추가할 경우 리스트를 스크롤하더라도 상위 리스트와 스크롤이 따로 적용되지 않는다.

* `android:nestedScrollingEnabled="true"` : 자식 RecyclerView 를 상/하로 스와이프 할 경우, 자식 리스트만 상/하로 움직임.
* `android:nestedScrollingEnabled="false"` : 자식 RecyclerView 를 상/하로 스와이프 하더라도, 자식 RecyclerView 가 부모 RecyclerView 와 분리된 것처럼 보이지 않고, 하나의 RecyclerView 처럼 상/하로 움직임.