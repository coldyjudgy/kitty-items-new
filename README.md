# [kitty-items-new](https://github.com/onflow/kitty-items): on master branch

## Error 1. Invalid project configuration: encoding/hex: invalid byte: U+0024 '$'
```
flow project deploy --network=testnet
>> Invalid project configuration: encoding/hex: invalid byte: U+0024 '$'
```

### 1.1. 상황
- 이전 명령어
```
# Replace these values with your own!
export FLOW_ADDRESS=0xabcdef12345689
export FLOW_PRIVATE_KEY=xxxxxxxxxxxx
```
- 사용자 환경변수인 주소/개인키를 전역변수로 설정

### 1.2. 원인
- ${환경변수}를 환경변수로 인식하지 못함
- hex에는 '$'가 없기 때문에 인코딩 에러

### 1.3. 해결
#### 1.31. ``flow.json`` 에서 환경변수 이용하지 말고, 주소/개인키 직접 입력
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

#### 1.32. CLI 업데이트
- ``flow version`` 확인
- [재설치](https://docs.onflow.org/flow-cli/install/) 


### 1.4. 분석
#### 1.41. tutorial이 업데이트되기 전에는 ``.env``에 주소/개인키 직접 입력

```
// .env

# The Flow address, Account Index and Private Key used by the deployer. Note that those are the same values as obtained from `Creating a new Flow Account on Testnet` section on
[kitty-items-js README](https://github.com/dapperlabs/kitty-items/blob/master/kitty-items-js/README.md)
ACCOUNT_ADDRESS=
ACCOUNT_KEY_IDX=0
ACCOUNT_PRIVATE_KEY=

# Flow testnet node
FLOW_NODE=https://access-testnet.onflow.org

# Core contracts from Flow: https://docs.onflow.org/core-contracts. These would need to be change accordingly
# if pointing to an environment different from Flow testnet.
FUNGIBLE_TOKEN_ADDRESS=9a0766d93b6608b7
NON_FUNGIBLE_TOKEN_ADDRESS=631e88ae7f1d7c20
```

#### 1.42. Flow CLI v0.15.0 업데이트
- flow/keys/decode/decode.go [추가](https://github.com/onflow/flow-cli/commit/8cb55b29ecbf06932464b52fea2123ea8339561f)
- 주소/개인키는 환경변수로 ``export``해서 ``flow.json``에서 디코딩
```go

// decode.go

package decode

import (
	"encoding/hex"
	"fmt"

	flowsdk "github.com/onflow/flow-go-sdk"
	"github.com/spf13/cobra"

	cli "github.com/onflow/flow-cli/flow"
)

var Cmd = &cobra.Command{
	Use:   "decode <public key>",
	Short: "Decode a public account key hex string",
	Args:  cobra.ExactArgs(1),
	Run: func(cmd *cobra.Command, args []string) {

		publicKey := args[0]

		publicKeyBytes, err := hex.DecodeString(publicKey)
		if err != nil {
			cli.Exitf(1, "Failed to decode public key: %v", err)
		}

		accountKey, err := flowsdk.DecodeAccountKey(publicKeyBytes)
		if err != nil {
			cli.Exitf(1, "Failed to decode private key bytes: %v", err)
		}

		fmt.Printf("  Public key: %x\n", accountKey.PublicKey.Encode())
		fmt.Printf("  Signature algorithm: %s\n", accountKey.SigAlgo)
		fmt.Printf("  Hash algorithm: %s\n", accountKey.HashAlgo)
		fmt.Printf("  Weight: %d\n", accountKey.Weight)
	},
}
```
