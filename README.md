# keysafe-sgx
# env setup
+ follow https://github.com/intel/linux-sgx#build-and-install-the-intelr-sgx-driver
+ install linux-sgx 
# build
+ download baidu sgx-sdk with the name incubator-teaclave-sgx-sdk
+ copy the code into incubator-teaclave-sgx-sdk/samplecode
+ make SGX_MODE=SW
# execute
+ cd bin
+ ln -s ../certs .
+ ./app
# test
+ download postman
+ click "My Workspace", "New", "Websocket Request (Beta)"
+ connect to "wss://127.0.0.1:12345/save"
+ use postman as a web front-end to communicate with enclave.