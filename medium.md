# Medium



ZeroGravity (сокращенно 0G) — это первая система доступности данных со встроенным слоем хранения общего назначения, супермасштабируемая и децентрализованная. Масштабируемость 0G основана на идее разделения рабочего процесса доступности данных на полосу публикации и полосу хранения . Передача больших объемов происходит на полосе хранения данных, которая поддерживается слоем хранения, обеспечивающим горизонтальную масштабируемость за счет хорошо продуманного разделения, в то время как полоса публикации гарантирует свойство доступности за счет консенсуса выборки доступности данных.

Уровень хранения 0G состоит из сети хранения, соединенной с отдельной сетью консенсуса. Каждый узел хранения активно участвует в процессе майнинга, отправляя доказательство доступности определенного фрагмента данных смарт-контракту, развернутому в сети консенсуса. Как только доказательство подтверждается смарт-контрактом, узел хранения получает соответствующее вознаграждение. Разбиение на разделы обеспечивается за счет того, что узел получает большее вознаграждение за хранение определенных данных, которые принадлежат к тому же разделу, что и этот узел.

Этот механизм, основанный на стимулах, вознаграждает узлы за вклад, а не наказывает их за неправильное поведение, поэтому он может лучше стимулировать узлы к участию в обслуживании сети и, следовательно, может способствовать достижению лучшей масштабируемости сети на практике. Хранилище 0G также спроектировано как общая система хранения данных с несколькими стеками абстракций и структур, включая слой журналов только с приложениями для архивирования неструктурированных данных, а также слой ключей-значений для управления изменяемыми и структурированными данными. Это позволяет 0G поддерживать надежное индексирование данных и большее разнообразие типов доступных данных из сценариев второго уровня и искусственного интеллекта.

**Установка 0G Storage node**

_**Рекомендуемые характеристики сервера:**_

_CPU : 4 cores_

_Memory : 16GB_

_Storage : 500GB / 1T NVMe SSD_

_Bandwidth : 500 Mbps for Download / Upload_

_**Обновляем пакеты:**_

```
sudo apt-get update
sudo apt-get install clang cmake build-essential
```

_**Устанавливаем GO:**_

```
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

_**Устанавливаем Rust:**_

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

_**Клонируем репозиторий:**_

```
git clone -b v0.5.1 https://github.com/0glabs/0g-storage-node.git
```

_**Build:**_

```
cd $HOME/0g-storage-node
git submodule update --init
sudo apt install cargo
cargo build --release
```

_**Устанавливаем зависимости:**_

```
echo 'export ZGS_CONFIG_FILE="$HOME/0g-storage-node/run/config.toml"' >> ~/.bash_profile
echo 'export ZGS_LOG_DIR="$HOME/0g-storage-node/run/log"' >> ~/.bash_profile
echo 'export ZGS_LOG_CONFIG_FILE="$HOME/0g-storage-node/run/log_config"' >> ~/.bash_profilesource ~/.bash_profile
```

_**Настройка переменных:**_

```
ENR_ADDRESS=$(wget -qO- eth0.me)
echo "export ENR_ADDRESS=${ENR_ADDRESS}"cat <<EOF >> ~/.bash_profile
export ENR_ADDRESS=${ENR_ADDRESS}
export ZGS_CONFIG_FILE="$HOME/0g-storage-node/run/config.toml"
export ZGS_LOG_DIR="$HOME/0g-storage-node/run/log"
export ZGS_LOG_CONFIG_FILE="$HOME/0g-storage-node/run/log_config"
EOFsource ~/.bash_profile
```

Далее направляемся к крану вставляем свой адрес и получаем тестовые токены. [_https://faucet.0g.ai/_](https://faucet.0g.ai/)

Если вы устанавливаете данный узел отдельно от ноды Валидатора то далее открываем свой Метамаск и копируем свой приватный ключ и вставляем его в следующую команду в поле Enter your private key

_**Вводим команду:**_

```
read -sp "Enter your private key: " PRIVATE_KEY && echo
sed -i 's|miner_key = ""|miner_key = "'"$PRIVATE_KEY"'"|' $HOME/0g-storage-node/run/config.toml
```

Если у нас установлена на сервера нода Валидатора и мы дополнительно ставим данную ноду то _**вводим следующее:**_

```
0gchaind keys unsafe-export-eth-key $WALLET_NAME
```

_**Добавляем директорию DB:**_

```
mkdir -p "$HOME/0g-storage-node/network" "$HOME/0g-storage-node/db"
```

_**Обновляем конфиг:**_

```
sed -i ‘s|^\s*#\?\s*network_dir\s*=.*|network_dir = "/root/0g-storage-node/network"|’ "$ZGS_CONFIG_FILE"
sed -i "s|^\s*#\?\s*network_enr_address\s*=.*|network_enr_address = \"$ENR_ADDRESS\"|" "$ZGS_CONFIG_FILE"
sed -i ‘s|^\s*#\?\s*network_enr_tcp_port\s*=.*|network_enr_tcp_port = 1234|’ "$ZGS_CONFIG_FILE"
sed -i ‘s|^\s*#\?\s*network_enr_udp_port\s*=.*|network_enr_udp_port = 1234|’ "$ZGS_CONFIG_FILE"
sed -i ‘s|^\s*#\?\s*network_libp2p_port\s*=.*|network_libp2p_port = 1234|’ "$ZGS_CONFIG_FILE"
sed -i ‘s|^\s*#\?\s*network_discovery_port\s*=.*|network_discovery_port = 1234|’ "$ZGS_CONFIG_FILE"
sed -i ‘s|^\s*#\?\s*network_target_peers\s*=.*|network_target_peers = 50|’ "$ZGS_CONFIG_FILE"
sed -i ‘s|^\s*#\?\s*blockchain_rpc_endpoint\s*=.*|blockchain_rpc_endpoint = "https://archive-0g.josephtran.xyz"|' "$ZGS_CONFIG_FILE"
sed -i 's|^\s*#\?\s*rpc_enabled\s*=\s*true|rpc_enabled = true|' "$ZGS_CONFIG_FILE"
sed -i 's|^\s*#\?\s*rpc_listen_address\s*=\s*"0.0.0.0:5678"|rpc_listen_address = "0.0.0.0:5678"|' "$ZGS_CONFIG_FILE"
sed -i 's|^\s*#\?\s*db_dir\s*=.*|db_dir = "/root/0g-storage-node/db"|' "$ZGS_CONFIG_FILE"
sed -i 's|^\s*#\?\s*log_config_file\s*=.*|log_config_file = "/root/0g-storage-node/run/log_config"|' "$ZGS_CONFIG_FILE"
sed -i 's|^\s*#\?\s*log_directory\s*=.*|log_directory = "/root/0g-storage-node/run/log"|' "$ZGS_CONFIG_FILE"
sed -i "s|^\s*#\?\s*miner_key\s*=.*|miner_key = \"$PRIVATE_KEY\"|" "$ZGS_CONFIG_FILE"
```

_**Обновляем конфиг**_

```
nano $HOME/0g-storage-node/run/config.toml
```

_network\_enr\_address =Ваш ип адрес сервера_

_network\_boot\_nodes =_ \[“/ip4/54.219.26.22/udp/1234/p2p/16Uiu2HAmTVDGNhkHD98zDnJxQWu3i1FL1aFYeh9wiQTNu4pDCgps”,”/ip4/52.52.127.117/udp/1234/p2p/16Uiu2HAkzRjxK2gorngB1Xq84qDrT4hSVznYDHj6BkbaE4SGx9oS”,”/ip4/18.162.65.205/udp/1234/p2p/16Uiu2HAm2k6ua2mGgvZ8rTMV8GhpW71aVzkQWy7D37TTDuLCpgmX”]

_log\_contract\_address =_ “0xbD2C3F0E65eDF5582141C35969d66e34629cC768”

_blockchain\_rpc\_endpoint_ “[https://og-testnet-jsonrpc.itrocket.net:443](https://og-testnet-jsonrpc.itrocket.net/)” и “[https://evmrpc-testnet.0g.ai](https://evmrpc-testnet.0g.ai/)” (посторонние rpc)

_mine\_contract\_address =_ “0x6815F41019255e00D6F34aAB8397a6Af5b6D806f”

_miner\_key =_ ваш приватный ключ EVM

_reward\_contract\_address =_ “0x51998C4d486F406a788B766d93510980ae1f9360”

_**Подключение к собственному RPC Валидатора**_

_**Открываем наш App.toml файл**_

```
nano $HOME/.0gchain/config/app.toml
```

_**Обновляем JSON-RPC**_

Меняем `127.0.0.1:8545` to `0.0.0.0:8545`.

Меняем `api = "eth,net,web3"` to `api = "eth,txpool,personal,net,debug,web3"`

Indexer – меняем с “null” на “kv”

Сохраняем Ctrl+X+Y и _**далее прописываем**_

```
sudo ufw allow 8545/tcpnano $HOME/0g-storage-node/run/config.toml
```

blockchain\_rpc\_endpoint — [http://127.0.0.1:8545](http://127.0.0.1:8545/)

_Перезагружаем Валидатор_

```
sudo systemctl stop 0gd && sudo systemctl daemon-reload && sudo systemctl enable 0gd && sudo systemctl
```

_**Создаем сервисный файл:**_

```
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target[Service]
User=root
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535[Install]
WantedBy=multi-user.target
EOF
```

Запускаем ноду:

```
sudo systemctl daemon-reload && \
sudo systemctl enable zgs && \
sudo systemctl start zgs
```

_**Логи**_

```
ls ~/0g-storage-node/run/log/
tail -f ~/0g-storage-node/run/log/zgs.log.$(date +%Y-%m-%d)
```

_**Рестарт ноды:**_

```
sudo systemctl restart zgs
```

_**Посмотреть ошибки:**_

```
grep "Error" $ZGS_LOG_DIR/zgs.log.*
```

_**Обновление ноды:**_

```
cd $HOME/0g-storage-node
git fetch
git checkout tags/<version>
git submodule update --init
cargo build --release
sudo mv $HOME/0g-storage-node/target/release/zgs_node /usr/local/bin
```

_**Удаление ноды:**_

```
sudo systemctl stop zgs
sudo systemctl disable zgs
sudo rm /etc/systemd/system/zgs.service
sudo rm /usr/local/bin/zgs_node
rm -rf $HOME/0g-storage-node
```

_Официальный сайт:_ [https://0g.ai/](https://0g.ai/)

_Twitter:_ [https://x.com/0G\_labs](https://x.com/0G\_labs)

_Discord:_ [https://discord.gg/SmC9GyGA](https://discord.gg/SmC9GyGA)
