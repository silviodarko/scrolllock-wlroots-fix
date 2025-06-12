# 💡 scrolllock-wlroots-fix

Fix para manter o LED do Scroll Lock aceso (ou piscando) em ambientes baseados em `wlroots` (Hyprland, Sway, etc), que bugam o controle de LEDs de teclado em alguns modelos (tipo o EMEET).  
Esse script aqui resolve esse perrengue com um Pythonzinho maroto e um serviço `systemd` que liga junto com o sistema. Simples, direto e sem enrolação.

---

## 🧠 O Problema

Alguns teclados (especialmente com layout ABNT ou teclas multimídia) ficam **sem o LED do Scroll Lock** funcional quando usamos ambientes gráficos com o **wlroots** (como o Hyprland ou Sway).  
No Ubuntu e derivados, isso geralmente funciona de boas, mas no Arch ou qualquer distro mais crua... **puff, sumiu o LED!** 😵‍💫

Então bolei esse esquema: um script que **força o estado do LED no device certo**, e sobe no boot com o `systemd`.

---

## ⚙️ O Que Esse Script Faz

1. Detecta e se conecta ao evento do teclado (`/dev/input/eventX`)
2. Usa a lib `python-libevdev` pra forçar o Scroll Lock LED a acender
3. Pode até piscar, se quiser um efeito boate (útil pra debug!)
4. Fica rodando em loop se quiser, ou só dá um toggle e sai

---

## 🚀 Instalação

### 1. Instale a dependência:

No Arch/Manjaro:
```bash
sudo pacman -S python-libevdev
No Debian/Ubuntu:

bash
Copiar
Editar
sudo apt install python3-libevdev
2. Clone este repositório:
bash
Copiar
Editar
git clone https://github.com/SEU-USUARIO/scrolllock-wlroots-fix.git
cd scrolllock-wlroots-fix
3. Teste o script manualmente
bash
Copiar
Editar
sudo python3 ledToggler.py /dev/input/event4 scrolllock scrolllock 1 1
⚠️ Troque /dev/input/event4 pelo seu teclado real (descubra com sudo evtest ou ls /dev/input/by-id).

🛠️ Instalando como serviço (systemd)
1. Copie o script pro sistema:
bash
Copiar
Editar
sudo mkdir -p /opt/scrolllock-wlroots-fix
sudo cp ledToggler.py /opt/scrolllock-wlroots-fix/
sudo chmod +x /opt/scrolllock-wlroots-fix/ledToggler.py
2. Crie o serviço:
bash
Copiar
Editar
sudo nano /etc/systemd/system/scrolllock-led.service
E cole:

ini
Copiar
Editar
[Unit]
Description=Scroll Lock LED fix for EMEET Keyboard
After=graphical.target

[Service]
Type=simple
ExecStart=/usr/bin/python3 /opt/scrolllock-wlroots-fix/ledToggler.py /dev/input/event4 scrolllock scrolllock 1 1
Restart=on-failure

[Install]
WantedBy=multi-user.target
⚠️ Não esquece de trocar o event4 pelo seu real, beleza?

3. Ative e inicie o serviço
bash
Copiar
Editar
sudo systemctl daemon-reload
sudo systemctl enable scrolllock-led.service
sudo systemctl start scrolllock-led.service
Quer saber se deu bom?

bash
Copiar
Editar
systemctl status scrolllock-led.service
🧼 Desinstalar
bash
Copiar
Editar
sudo systemctl stop scrolllock-led.service
sudo systemctl disable scrolllock-led.service
sudo rm /etc/systemd/system/scrolllock-led.service
sudo rm -rf /opt/scrolllock-wlroots-fix
✨ Créditos
Essa gambiarra abençoada surgiu da frustração de ver um LED apagado e do espírito livre do Linux.
Feito com carinho por Silvada (a.k.a. Riquelab), com a ajuda espiritual do ChatGPT e o apoio moral da comunidade Arch 🐧.

Se te ajudou, dá um star aí ⭐ e espalha o Linux com alegria!

🐧 Viva o Linux! 🦾