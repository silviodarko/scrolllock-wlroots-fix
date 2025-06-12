# 💡 scrolllock-wlroots-fix

Fix para manter o LED do Scroll Lock aceso (ou piscando) em ambientes baseados em **wlroots** (Hyprland, Sway, etc.), que podem bugar o controle de LEDs de teclado em alguns modelos (ex: EMEET).

Este script Python junto com um serviço **systemd** resolvem o problema, ativando junto com o sistema. Simples, direto e na moral.

---

## 🧠 O Problema

Alguns teclados (especialmente com layout ABNT ou teclas multimídia) podem ter o **LED do Scroll Lock inoperante** ao usar ambientes gráficos com **wlroots** (como Hyprland ou Sway).

Em distribuições tipo Ubuntu, geralmente funciona bem. Mas em sistemas mais “crus” como Arch Linux, o LED pode simplesmente não acender, deixando a galera na mão.

Esse projeto oferece uma solução prática: um script que força o estado do LED no dispositivo correto e que é iniciado no boot via systemd.

---

## ⚙️ O Que Este Script Faz

- Detecta e se conecta ao evento do teclado (`/dev/input/eventX`).
- Usa a biblioteca `python-libevdev` para forçar o Scroll Lock LED a acender.
- Permite configurar o LED para piscar, se quiser (ótimo para debug).
- Pode rodar em loop contínuo ou só realizar o toggle e sair — você escolhe.

---

## 🚀 Instalação

### 1. Instale a dependência:

**No Arch/Manjaro:**

    sudo pacman -S python-libevdev

**No Debian/Ubuntu:**

    sudo apt install python3-libevdev

### 2. Clone o repositório:

    git clone https://github.com/silviodarko/scrolllock-wlroots-fix.git
    cd scrolllock-wlroots-fix

### 3. Teste o script manualmente:

    sudo python3 ledToggler.py /dev/input/eventX scrolllock scrolllock 1 1

⚠️ **Importante:** Troque `/dev/input/eventX` pelo identificador real do seu teclado (descubra com `sudo evtest` ou olhando em `/dev/input/by-id`).

---

## 🛠️ Instalando como Serviço (systemd)

Para garantir que o fix rode automaticamente no boot:

### 1. Copie o script para o sistema:

    sudo mkdir -p /opt/scrolllock-wlroots-fix
    sudo cp ledToggler.py /opt/scrolllock-wlroots-fix/
    sudo chmod +x /opt/scrolllock-wlroots-fix/ledToggler.py

### 2. Crie o arquivo do serviço systemd:

    sudo nano /etc/systemd/system/scrolllock-led.service

Cole o conteúdo abaixo no arquivo aberto:

    [Unit]
    Description=Scroll Lock LED fix for Keyboard
    After=graphical.target network.target

    [Service]
    Type=simple
    ExecStart=/usr/bin/python3 /opt/scrolllock-wlroots-fix/ledToggler.py /dev/input/eventX scrolllock scrolllock 1 1
    Restart=on-failure
    StandardOutput=journal
    StandardError=journal

    [Install]
    WantedBy=multi-user.target

⚠️ **Não esqueça de substituir** `/dev/input/eventX` pelo seu dispositivo correto!

_Removi menção específica à EMEET para deixar mais genérico, já que pode funcionar com vários teclados. Também incluí `network.target` e saída para `journal` para facilitar o debug._

### 3. Ative e inicie o serviço:

    sudo systemctl daemon-reload
    sudo systemctl enable scrolllock-led.service
    sudo systemctl start scrolllock-led.service

### 4. Verifique o status do serviço:

    systemctl status scrolllock-led.service

---

## 🧼 Desinstalação

Se quiser remover tudo depois:

    sudo systemctl stop scrolllock-led.service
    sudo systemctl disable scrolllock-led.service
    sudo rm /etc/systemd/system/scrolllock-led.service
    sudo rm -rf /opt/scrolllock-wlroots-fix

---

## ✨ Créditos

Essa solução nasceu da frustração com um LED apagado e do espírito “faça você mesmo” do Linux.

Feito com carinho por **Silviodarko** (a.k.a. Riquelab), com a ajuda espiritual do ChatGPT e o apoio moral da comunidade Arch Linux 🐧.

Se esse projeto te salvou, deixa uma estrela ⭐ no repositório e espalhe a vibe do Linux pelo mundão!

🐧 **Viva o Linux!** 🦾
