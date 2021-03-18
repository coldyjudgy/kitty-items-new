# kitty-items-new: on master branch

## Error 1. Invalid project configuration: encoding/hex: invalid byte: U+0024 '$'
```
flow project deploy --network=testnet
>> Invalid project configuration: encoding/hex: invalid byte: U+0024 '$'
```

### 1.1 상황
- 이전 명령어
```
# Replace these values with your own!
export FLOW_ADDRESS=0xabcdef12345689
export FLOW_PRIVATE_KEY=xxxxxxxxxxxx
```
- 사용자 환경변수인 주소와 개인키 값을 전역변수로 설정

### 1.2 원인
- idk.. yet

### 1.3 해결
- ``flow.json`` 에서 환경변수 이용하지 말고, 주소/개인키 값 직접 입력
- 변경 전
```
    "testnet-account": {
      "address": "${FLOW_ADDRESS}",
      "keys": "${FLOW_PRIVATE_KEY}"
```

- 변경 후
```
"testnet-account": {
      "address": "YOUR FLOW_ADDRESS",
      "keys": "YOUR FLOW_PRIVATE_KEY"
```

### 1.4 분석
