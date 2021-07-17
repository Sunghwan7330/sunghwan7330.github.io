---
title: "gdb define 사용하기"
excerpt: "gdb define 을 이용하여 함수 정의하는 방법에 대해 간단하게 정리합니다."

categories:
  - gdb
tags:
  - gdb
  - tip

toc: true
toc_sticky: true

date: 2021-07-16
last_modified_at: 2021-07-16
---

# 개요 

gdb 에서 define을 사용하면 반복적인 작업을 편하게 할 수 있습니다. 

해당 글은 제가 gdb 에서 다른 define를 사용하게 되면 계속 추가하여 업로드할 예정입니다. 

# define 사용 예시 

```
define check_arr
  set $idx=0
  while ($idx < 2048)
    print arr[$idx]
    set $idx=$idx + 1
  end
end
```

위와 같이 입력시 check_arr 함수가 정의됩니다. 

check_arr 함수는 0 ~ 2048 까지의 arr 배열을 출력하는 함수입니다. 

