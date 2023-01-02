# dev environment setup and build guide
## setup without docker
### install sgx-rust-sdk
+ https://github.com/intel/linux-sgx#build-and-install-the-intelr-sgx-driver
### install linux-sgx 
+ https://github.com/intel/linux-sgx#introduction
### install sgx-ssl
+ https://github.com/intel/intel-sgx-ssl
### build
+ cd <sgx-rust-sdk>/samplecode
+ git clone https://github.com/keysafe-protocol/keysafe-app.git
+ set environment:
  + SGX_SDK_RUST 
  + SGX_SDK
  + SGX_MODE # set to SW if you don't have SGX support
  + SGXSSL_CRYPTO
### unit test
```
   cd app
   cargo test
```
### build ks-sgx
```
  git clone https://github.com/keysafe-protocol/keysafe-sgx
  cd ks-sgx; make
```
### build webapp with sgx
```
  cd keysafe-app; make
```
### execute
```
  cd bin
  ln -s ../certs .
  ./app
```
## setup with docker
+ install docker
+ build mysql db docker instance 
```
  docker pull mysql:latest 
  docker run --name ks-db -p 12345:3306 -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=ks123 -d mysql:latest
```
+ login mysql docker instance to setup db 
```
  docker exec -it ks-db bash
  # inside docker, create db, user and schema
  mysql -h localhost -u root -pks123
  copy app/schema.sql to mysql shell
  exit # mysql shell
  exit # mysql docker instance
```
+ build keysafe-app docker instance
```
  git clone https://github.com/keysafe-protocol/keysafe-app.git
  cd keysafe-app
  git checkout polkadot
  cd docker
  docker build -t ks01 -f Dockerfile .
  cd ..
  docker run --network host -v ${PWD}:/root/incubator-teaclave-sgx-sdk/samplecode/keysafe-app -ti ks01
```
+ inside docker instance, build package
```
  bash /root/get_ks_sgx.sh
  cd /root/incubator-teaclave-sgx-sdk/samplecode/keysafe-app/;
  make -f MakeHwFile SGX_MODE=SW 
```
+ inside docker instance, run unit test
```
  cd /root/incubator-teaclave-sgx-sdk/samplecode/keysafe-app/app; cargo test
```
+ inside docker instance, prepare environment before start up
```
  cd /root/incubator-teaclave-sgx-sdk/samplecode/keysafe-app/bin;
  ../scripts/prepare_bin.sh
```
+ start service, once started, ./app stucks waiting for requests
```
  export KS_ACCOUNT="zone envelope fish dolphin cup conduct burden tomato uphold final wood dune"
  ./app &
```
+ start front-end 
```
  cd <a-new-directory>
  git clone https://github.com/keysafe-protocol/keysafe-front
  git checkout polkadot
  docker build -t keysafe-frontend .
  docker run --rm -p 3000:3000 -e REACT_APP_BASE_URL='https://<your-ip-address>:30000/ks' keysafe-frontend
```
+ visit http://your-ip-address:3000 to open the website

## use case
+ click login for the first time
+ <img width="274" alt="image" src="https://user-images.githubusercontent.com/1289853/210266460-916595b4-b684-45d4-92b0-044263a4aad0.png">

+ input your email account
+ <img width="464" alt="image" src="https://user-images.githubusercontent.com/1289853/210266518-19591001-1d0f-428f-8aae-ad715569908c.png">

+ when running in dev mode ( by default in conf.toml ), email will not be sent, confirm code is recorded in bin/logs/ks.log
+ when running in production mode, email will be sent using official email account with a confirm code.

+ use your confirm code to login your email account

+ when saving your secret key, you will have 4 options, email/passwd/gauth/oauth@github.
+ <img width="276" alt="image" src="https://user-images.githubusercontent.com/1289853/210266717-5dd80cf4-0971-4d80-be5a-0f7432287088.png">
  + email/passwd/gauth will be available by default
  + <img width="446" alt="image" src="https://user-images.githubusercontent.com/1289853/210266792-c14f263b-4cad-4dc4-b034-6e4a5fee41bc.png">
  + <img width="596" alt="image" src="https://user-images.githubusercontent.com/1289853/210266822-3409392d-2614-4507-9f95-5252b7ba004b.png">
  + you need a client-id and client-secret to use oauth@github.
+ when recovering your secret key, prove yourself with 2 of the 3 conditions above, e.g. email + passwd or email + google-auth.
