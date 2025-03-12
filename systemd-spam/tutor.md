## Install nodejs

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
source ~/.bashrc
nvm install 'lts/*'
nvm use 'lts/*'
```

## Buat directory + file

```
mkdir bolicloop
cd bolicloop
```

```
nano loop.js
```

### Isi pake ini

```
for (let i = 0; i < 1000; i++) {
    const url = 'https://api.hyperbolic.xyz/v1/chat/completions';
    
    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        Authorization: 'Bearer API_KEY_MU',
      },
      body: JSON.stringify({
        model: 'deepseek-ai/DeepSeek-V3',
        messages: [
          {
            role: 'system',
            content: 'you are blockchain expert'
          },
          {
            role: 'user',
            content: 'make a random question about Tendermint, Staking, PoS, or PoW tech without answer'
          }
        ],
        max_tokens: 1024,
        temperature: 1,
        top_p: 1,
        stream: false
      }),
    });
    
    const json = await response.json();
      const output = json.choices[0].message.content;
      console.log(`Pertanyaan ${i + 1}:`);
      console.log(output);
      console.log('------------------------');
    }
  
```

- Bagian `API_KEY_MU` ganti pake API KEY mu di https://app.hyperbolic.xyz/settings
- model: 'deepseek-ai/DeepSeek-V3' bisa ganti pake model yg text apapun suka2

## Buat service

```
sudo tee /etc/systemd/system/bolicloop.service > /dev/null << EOF
[Unit]
Description=Loop Chat
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=2
ExecStart=$(which node) $HOME/bolicloop/loop.js
[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable bolicloop
sudo systemctl start bolicloop
```

### Cek jalan apa ngga

```
sudo journalctl -u bolicloop -f --no-hostname -o cat
```