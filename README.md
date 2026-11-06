<p align="center">
  <img alt="Python" src="https://img.shields.io/badge/Python-libevdev-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img alt="Linux" src="https://img.shields.io/badge/Linux-Input_Devices-17233A?style=for-the-badge&logo=linux&logoColor=white"/>
  <img alt="Status" src="https://img.shields.io/badge/Status-Community_Workaround-C9A86A?style=for-the-badge"/>
</p>

# Scroll Lock LED | Linux Input Workaround

Projeto experimental em **Python + libevdev** para controlar o LED de teclados Linux quando o estado de Scroll Lock não acompanha o comportamento esperado em certas sessões Wayland.

Feito para estudar eventos de entrada e solucionar uma situação real de periférico. **Não é uma modificação do compositor** e não depende de uma integração específica com Niri ou Noctalia.

## Como funciona

O script [ledToggler.py](./ledToggler.py) abre um dispositivo de eventos do Linux, identifica a funcionalidade de LED suportada pelo dispositivo e observa eventos de teclado para atualizar o indicador. A implementação pode permanecer executando enquanto acompanha os eventos.

| Arquivo | Finalidade |
| --- | --- |
| [ledToggler.py](./ledToggler.py) | Leitura de eventos via libevdev e tentativa de controlar indicadores LED |
| [scrolllock-led.service](./scrolllock-led.service) | Modelo de serviço systemd com caminho de instalação e dispositivo que **precisam ser conferidos** |

## Requisitos

- Linux com interfaces de dispositivos de entrada compatíveis.
- Python 3 e biblioteca Python **libevdev**.
- Teclado expondo os eventos e indicadores LED necessários.
- Permissão apropriada para o dispositivo selecionado.

**Importante:** dispositivos de entrada como /dev/input/event4 podem mudar após reinicialização ou reconexão. O exemplo de serviço contém **/dev/input/eventX como marcador que você deve substituir**; não o habilite sem identificar o dispositivo correto. Sempre que possível, prefira um caminho persistente existente sob /dev/input/by-id.

## Fluxo seguro de teste

Primeiro, clone e inspecione os arquivos, sem instalar serviço algum:

    git clone https://github.com/silviodarko/scrolllock-wlroots-fix.git
    cd scrolllock-wlroots-fix
    cat ledToggler.py
    cat scrolllock-led.service

Antes de qualquer execução, identifique seu teclado, confira as permissões e a presença de indicadores LED. A biblioteca libevdev não transforma um teclado incompatível em um dispositivo com suporte a LEDs.

Teste manualmente em sessão recuperável; apenas depois considere adaptar o serviço incluído. O serviço assume a cópia do script para **/opt/scrolllock-wlroots-fix/ledToggler.py**. Edite o dispositivo no ExecStart e revise os privilégios antes de ativar.

### Limitações conhecidas

- A busca de LEDs no script pressupõe que a entrada correspondente exista em /sys/class/leds; dispositivos sem correspondência podem causar erro.
- Alguns ambientes gráficos e teclados tratam o estado de Scroll Lock de forma diferente; **não há garantia universal**.
- O projeto não inclui testes automatizados de compatibilidade com periféricos nem pacote oficial de distribuição.
- Não deixe um leitor de eventos de teclado ativo sem compreender e confiar na implementação e nos privilégios exigidos.

## Outros projetos

Desenvolvido por **[Sílvio Henrique](https://github.com/silviodarko)**. Confira também o [Radeon RX 580 Linux Stability Lab](https://github.com/silviodarko/rx580-linux-stability-fix) e o [roadmap público do SilvOS](https://github.com/silviodarko/silviodarko/blob/main/SILVOS_ROADMAP.md).

Relatos de compatibilidade, propostas de correção e melhorias documentadas são bem-vindos.
