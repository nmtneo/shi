Yêu cầu: Install Celetis Light node Install git Install Golang

1/ Cập nhật phiên bản mới:

sudo apt update && sudo apt upgrade -y

2/ Bổ sung package cần thiết:

sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential git make ncdu -y

3/ Cài đặt Golang:

ver="1.19.1"

cd $HOME

wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"

sudo rm -rf /usr/local/go

sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"

rm "go$ver.linux-amd64.tar.gz"

Chuyển thư mục Golang:

echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
Kiểm tra phiên bản: go version

4/ Cài đặt Light Node:

cd $HOME rm -rf celestia-node git clone https://github.com/celestiaorg/celestia-node.git

cd celestia-node/ git checkout tags/v0.8.1 make build

make install

make cel-key

5/ Cài đặt mạng:

celestia light init --p2p.network blockspacerace

Chạy node bằng RPC của dự án, dùng port 26659. celestia light start --core.ip https://rpc-blockspacerace.pops.one --keyring.accname my_celes_key --gateway --gateway.addr localhost --gateway.port 26659 --p2p.network blockspacerace --metrics.tls=false --metrics --metrics.endpoint otel.celestia.tools:4318

Tiếp theo ta cần Ethermint (EVM) rollup ( bạn có thể xem thêm tai đây: https://rollkit.dev/docs/tutorials/ethermint/)

Chúng ta sẽ sử dụng ethermint từ kho lưu trữ của celestia

Step1: Clone từ từ git -->

git clone https://github.com/celestiaorg/ethermint.git image
Step 2: cd vào thư mục ethermint

cd ethermint
make install image
Step 3: bạn sẽ thấy trong thư mục ethermint sẽ có lệnh bash, chúng ta sẽ khởi chạy nó để khởi tạo một sổ bản tổng hợp ethermint cục bộ trên Celestia

bash init.sh
Và kết quả:
![image](https://github.com/nmtneo/shi/assets/94493542/ec9bf136-228e-4e77-ba8d-145049d52f76)


Step 4: tiến hành thiết lập môi trường:

NAMESPACE_ID=$(openssl rand -hex 8) DA_BLOCK_HEIGHT=$(curl https://rpc-blockspacerace.pops.one/block | jq -r '.result.block.header.height')

Kết quả: image

Step 5: Khi quá trình thiết lập này hoàn tất, giờ đây chúng ta có thể bắt đầu Tổng hợp Ethermint của mình

ethermintd start --rollkit.aggregator true --rollkit.da_layer celestia --rollkit.da_config='{"base_url":"http://38.242.249.168:26658","timeout":60000000000,"gas_limit":6000000,"fee":6000}' --rollkit.namespace_id $NAMESPACE_ID --rollkit.da_start_height $DA_BLOCK_HEIGHT
Note: bạn cần thay đổi IP của bạn

Kết quả: image

