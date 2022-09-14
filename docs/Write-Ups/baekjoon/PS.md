# Problem Solving Tactics

## Graph

### Minimum Spanning Tree

- Use Kruskal's algorithm.
- Use Union & Find

### 사이클 판정법

- Use 위상정렬
![원리](https://codingdog.tistory.com/entry/%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%9C%84%EC%83%81-%EC%A0%95%EB%A0%AC%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%98%EB%A9%B4-%EA%B7%B8%EB%9E%98%ED%94%84-%EC%82%AC%EC%9D%B4%ED%81%B4%EC%9D%B4-%EC%9E%88%EB%8A%94%EC%A7%80-%ED%8C%90%EB%8B%A8%ED%95%A0%EA%B9%8C%EC%9A%94)
1. Indegree 가 0인 애들을 queue에 넣는다
2. front 부터 0인 애들 지우면서 그 노드가 뻗은 간선이 만들어 낸 indegree를 빼준다
3. 새로 indegree 정보가 업데이트되면 1번으로 다시 돌아간다.
4. 더 이상 업데이트가 되지 안되는데 살아있는 애들은 cycle, 못 살아난 아이들은 not cycle

## Math

### 소수판정법

- Use 아리스토텔레스의 체
- O(nlglgn)을 위해 while(n){for(i = n^2)} 이런식으로 체를 걸러낸다
`
### 선분 교차 판정법

- use CCW(Counterclockwise)
> CCW란 삼각형의 면적을 구하는 공식으로
![선분판정법](https://t1.daumcdn.net/cfile/tistory/99AE303359DB53141F)
의 식이 S 가 0보다 크면 반시계 방향, S 가 0 보다 작으면 시계방향, 0이면 평행인 점을 이용하면 된다.

- 만약 하나의 선분과 두점에 대한 각각의 CCW가 부호가 반대라면 서로가 교차한다는 것.
- 하지만 예외 케이스 존재한다.
1.  두 선분이 교차하게 평행이거나 
2. 교차하지 않은채 CCW부호가 마이너스 일수 있다.

1의 경우 점들의 위치를 확인해 교차여부를 확인하면 되고
2의 경우 CCW(A,B,C) * CCW(A,B,D) <= 0 && CCW(C,D,A) * CCW(C, D, B) <= 0 , 즉 둘다 서로 다른 부호이면 된다. 

## MISC

### 특정 수가 몇개 존재하는 지 알기 위해서 
- Use Lower bound, upper bound
=> 정렬된 array에 upperbound - lower bound 시 몇개 있는지 확인할 수 있다. 


