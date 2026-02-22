# Wazuh-Lab
Wazuh SIEM Lab: Implementação, Monitoramento e Resposta Ativa

A ideia desse projeto é simular um ambiente empresarial onde posso implementar o Wazuh e criar configurações personalizadas

<h1>💻Instalação:</h1>
(Todas as informações obtidas nesse tópico de instalação estão no site oficial do Wazuh.
<h3>Wazuh Server:</h3>

Escolhi uma VM do Kali Linux instalada na virtualbox para ser o servidor mas você pode usar qualquer sistema operacional com ênfase nos documentados no site do Wazuh.

1°- ```curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a```

  Com isso o terminal do linux irá baixar o wazuh (pode demorar um pouco) e no final irá aparecer a mensagem:
  ```
  INFO: --- Summary ---
INFO: You can access the web interface https://<WAZUH_DASHBOARD_IP_ADDRESS>
    User: admin
    Password: <ADMIN_PASSWORD>
INFO: Installation finished.
  ```
  <WAZUH_DASHBOARD_IP_ADDRESS> = IP do seu servidor + Porta HTTPS 
  
  Exemplo: https://192.168.15.46:443

  <ADMIN_PASSWORD> = É uma senhas aleatória e você DEVE salvá-la mas se perdê-la vá até o arquivo: wazuh-passwords.txt
  
2°- Basta abrir o servidor web com o seu IP <WAZUH_DASHBOARD_IP_ADDRESS>:

<img width="1919" height="969" alt="image" src="https://github.com/user-attachments/assets/7b2facba-e2c4-451d-a252-2fdaa6a62c9d" />

Ele irá abrir essa tela e basta colocar o admin e a senha gerada para entrar na dashboard.

Com isso, o dashboard irá aparecer sem nenhum agente configurado ainda (No meu caso tem o Windows)
<img width="1919" height="967" alt="image" src="https://github.com/user-attachments/assets/3b6fdbe8-76fc-421a-91f9-f08e15603a7e" />

Agora vamos configurar o agente a ser monitorado.

<h3>Wazuh Agent:</h3>
Existem vários sistemas operacionais que podem ser utilizados como Agent no Wazuh:

<img width="1117" height="157" alt="image" src="https://github.com/user-attachments/assets/7b0669d9-35c8-4d1d-b352-aadecaa4f9fb" />

<h4> Windows Agent: </h4>
Vá para o site do Wazuh e baixe o respectivo programa para instalar o agente : wazuh-agent-4.14.3-1.msi

Você pode executar ele e seguir com o gui: 

<p align="center">
<img width="351" height="307" alt="image" src="https://github.com/user-attachments/assets/ecc08f10-9a7b-4bbd-8d81-d38431b85bb3" />
<p>

Coloque o IP do Wazuh Server em Manager IP e em seguida vá em Manage > Start.

Com isso já está configurado seu Agent via GUI.

Se optar pelo CLI pelo CMD use:

```
wazuh-agent-4.14.3-1.msi /q WAZUH_MANAGER="192.168.15.46"
```
Onde o IP é o do seu servidor MAS TOME CUIDADO!!! 🛑

Mesmo após essa configuração, no meu Windows acabou não funcionando.

Para resolver vá até o arquivo ```C:\Program Files (x86)\ossec-agent\ossec.conf```
Abra com o notepad no modo Administrador e mude o IP manualmente se necessário:
```
<ossec_config>

  <client>
    <server>
      <address>192.168.15.46</address>
      <port>1514</port>
      <protocol>tcp</protocol>
    </server>
    <config-profile>windows, windows10</config-profile>
    <crypto_method>aes</crypto_method>
    <notify_time>20</notify_time>
    <time-reconnect>60</time-reconnect>
    <auto_restart>yes</auto_restart>
  </client>
```

<h3>Ubuntu Agent:</h3>

Instale os seguintes pacotes:
```
sudo apt-get install gnupg apt-transport-https
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/tmp/wazuh.gpg --import && sudo mv /tmp/wazuh.gpg /usr/share/keyrings/wazuh.gpg && sudo chmod 644 /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt-get update
```

TODOS os comandos acima precisam de permissão sudo em algum momento então certifique de ter acesso ao sudo de alguma forma.

Só colocar o servidor agora:

```
sudo WAZUH_MANAGER="192.168.15.46" apt-get install wazuh-agent
```
E rode o programa:
```
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

E PRONTO!!!

Agora temos o Windows e o Linux Agent configurados e prontos para serem monitorados no Wazuh!!!
<img width="1147" height="347" alt="image" src="https://github.com/user-attachments/assets/ae91840f-5c37-4c66-8b12-7f53a62b80c9" />

Diagrama da rede feita no Cisco Packet Tracer:

<p align="center">
<img width="584" height="418" alt="image" src="https://github.com/user-attachments/assets/beb87f6b-3b90-4be5-9b92-00a3e7e1c614" />
<p>
(Representação aproximada do que seria o que o virtualbox faz com as VMs que estão todas no modo bridge)

<img width="1919" height="959" alt="image" src="https://github.com/user-attachments/assets/21810850-7349-488b-9dd7-1f995f075e70" />
