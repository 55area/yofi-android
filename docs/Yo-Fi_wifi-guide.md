
--------------------------------------------------------------------------------

an android app that performs a network test, have to ask for permission to access internet, or can it be a default permission on install
------------------------------

For a normal Android app that performs an Internet/network test, **you do not need to show a runtime permission dialog asking for Internet access**.

`android.permission.INTERNET` is classified by Android as a **normal permission**. Once declared in the manifest, it is automatically granted when the app is installed. ([Android Developers][1])

Your `AndroidManifest.xml` can simply contain:

```xml
<manifest ...>

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        ...>
        ...
    </application>

</manifest>
```

`ACCESS_NETWORK_STATE` is also a normal permission and is automatically granted. ([Android Developers][2])

### For a network-testing app

This means the user can install your app and immediately press something like:

```text
TEST CONNECTION
       ↓
Download test
Upload test
Latency test
       ↓
Results
```

There is **no "Allow this app to access the Internet?" dialog**.

One distinction is important: if your network test specifically accesses **devices on the local network**, newer Android versions introduce additional local-network privacy protections. That is different from ordinary Internet access. ([Android Developers][3])

So for a conventional Internet speed/latency test:

**`INTERNET` → declare in Manifest → automatically granted → no runtime permission request.** ([Android Developers][2])

[1]: https://developer.android.com/reference/android/Manifest.permission?utm_source=chatgpt.com "Manifest.permission  |  API reference  |  Android Developers"
[2]: https://developer.android.com/develop/connectivity/network-ops/connecting?hl=pt-br&utm_source=chatgpt.com "Conectar à rede  |  Connectivity  |  Android Developers"
[3]: https://developer.android.com/about/versions/16/behavior-changes-16?utm_source=chatgpt.com "Behavior changes: Apps targeting Android 16 or higher  |  Android Developers"

--------------------------------------------------------------------------------

what other permissions can I put on manifest, to be default and not having to ask permission after install 
------------------------------


Yes. Android has a substantial set of **normal permissions** that can be declared in the manifest and are automatically granted at installation. You do not need a runtime permission dialog for these. ([Android Developers][1])

For a network-testing app, the particularly relevant ones are:

| Permission                   | Runtime dialog? | Purpose                                         |
| ---------------------------- | --------------- | ----------------------------------------------- |
| `INTERNET`                   | **No**          | Open network sockets / communicate with servers |
| `ACCESS_NETWORK_STATE`       | **No**          | Determine whether network connectivity exists   |
| `ACCESS_WIFI_STATE`          | **No**          | Obtain information about Wi-Fi                  |
| `WAKE_LOCK`                  | **No**          | Keep CPU awake during a test                    |
| `VIBRATE`                    | **No**          | Haptic feedback                                 |
| `RECEIVE_BOOT_COMPLETED`     | **No**          | Receive the boot-completed broadcast            |
| `HIGH_SAMPLING_RATE_SENSORS` | **No**          | High-frequency sensor data                      |

These are classified as `normal` in the Android permission reference. ([Android Developers][2])

For example, your network tester could have:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- Network testing -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

    <!-- Keep CPU running during a long test -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />

    <application
        android:label="@string/app_name"
        android:theme="@style/Theme.MyApp">
        ...
    </application>

</manifest>
```

### Important distinction

There are three broad categories:

**1. Normal → automatically granted**

```text
INTERNET
ACCESS_NETWORK_STATE
ACCESS_WIFI_STATE
WAKE_LOCK
VIBRATE
```

No runtime dialog.

**2. Dangerous → runtime request**

Examples:

```text
CAMERA
RECORD_AUDIO
ACCESS_FINE_LOCATION
ACCESS_COARSE_LOCATION
READ_CONTACTS
READ_CALENDAR
```

These require the app to request access while running on modern Android. ([Android Developers][1])

**3. Special/restricted → separate system authorization**

Some capabilities don't follow the ordinary runtime-permission model and may require the user to explicitly enable something in Settings or may be restricted to particular types of apps.

### For your network-test app

You can therefore keep the basic app **completely permission-dialog-free** if it only needs Internet connectivity and network information:

```text
INSTALL
   ↓
No permission dialog
   ↓
Open app
   ↓
Run network test
   ↓
Internet / Wi-Fi / latency / download / upload
```

One caveat for **Android 16/API 36+ and especially Android 17/API 37+**: if your test needs to actively discover or communicate with **devices on the user's local network**, `ACCESS_LOCAL_NETWORK` is now a separate consideration and is classified as dangerous. Ordinary Internet speed testing against an Internet server is different. ([Android Developers][2])

For a conventional Speedtest-like application, I would start with just **`INTERNET` + `ACCESS_NETWORK_STATE` + `ACCESS_WIFI_STATE`**, and add `WAKE_LOCK` only if the test actually needs it.

[1]: https://developer.android.com/training/permissions/requesting?utm_source=chatgpt.com "Request runtime permissions  |  Privacy  |  Android Developers"
[2]: https://developer.android.com/reference/android/Manifest.permission?utm_source=chatgpt.com "Manifest.permission  |  API reference  |  Android Developers"

--------------------------------------------------------------------------------

what permissions do I need to configure, so my app can read a qr-code, identify a new wi-fi and set the wi-fi access in phone
------------------------------


For the workflow you described:

1. **Scan a QR code** containing Wi-Fi credentials.
2. Extract `SSID`, security type, and password.
3. Connect the phone to that Wi-Fi network.

Android treats these as separate capabilities.

### 1. QR-code scanning

If you use the **camera** to scan the QR code, you need:

```xml
<uses-permission android:name="android.permission.CAMERA" />
```

`CAMERA` is a runtime permission, so the app must ask the user for camera access.

If you use an Android/system QR scanner that returns the scanned result to your app, you may be able to avoid requesting `CAMERA` yourself, depending on the implementation.

### 2. Accessing nearby Wi-Fi

For modern Android, declare:

```xml
<uses-permission
    android:name="android.permission.NEARBY_WIFI_DEVICES"
    android:usesPermissionFlags="neverForLocation" />
```

For an app targeting Android 13/API 33+, `NEARBY_WIFI_DEVICES` is a **runtime permission**. Android presents it under the **Nearby devices** permission group. ([Android Developers][1])

For older Android versions, you may also need:

```xml
<uses-permission
    android:name="android.permission.ACCESS_FINE_LOCATION"
    android:maxSdkVersion="32" />
```

This is particularly relevant if you actually scan/list nearby Wi-Fi networks using `WifiManager.startScan()` or `getScanResults()`. Those APIs have additional requirements. ([Android Developers][2])

### 3. Connecting to the Wi-Fi from the QR code

This is the important part.

On modern Android, **you cannot simply give your application unrestricted control over the phone's Wi-Fi settings**. Android deliberately limits this.

For Android 10+, there are several APIs:

| Goal                                    | API                                     | User interaction                      |
| --------------------------------------- | --------------------------------------- | ------------------------------------- |
| Connect temporarily to a specific Wi-Fi | `WifiNetworkSpecifier`                  | System-controlled connection request  |
| Suggest Wi-Fi for Internet connectivity | `WifiNetworkSuggestion`                 | System controls acceptance            |
| Add a Wi-Fi network to saved networks   | `ACTION_WIFI_ADD_NETWORKS`              | **User approves it**                  |
| Arbitrarily modify Wi-Fi settings       | Old `WifiManager.addNetwork()` approach | Not available to ordinary modern apps |

Android specifically recommends `ACTION_WIFI_ADD_NETWORKS` when the objective is adding a network to the user's saved Wi-Fi networks. The system presents the user with the approval flow. ([Android Developers][3])

`WifiNetworkSuggestion` can also provide credentials for a network, but those suggestions aren't ordinary user-owned saved networks. ([Android Developers][4])

### So your ideal workflow could be

```text
                 QR CODE
                    │
                    ▼
             ┌─────────────┐
             │ Scan QR code│
             └──────┬──────┘
                    │
             SSID + password
                    │
                    ▼
          ┌───────────────────┐
          │ Validate Wi-Fi    │
          │ configuration     │
          └─────────┬─────────┘
                    │
                    ▼
       Android Wi-Fi system API
                    │
                    ▼
          ┌───────────────────┐
          │ User approves     │
          │ connection/add    │
          └─────────┬─────────┘
                    │
                    ▼
                Connected
```

For your particular app, I'd **avoid doing a Wi-Fi scan at all if the QR code already contains the SSID and credentials**. You don't need to discover the network if the QR code already tells you which network to connect to.

That potentially reduces your permission requirements to approximately:

```xml
<uses-permission android:name="android.permission.CAMERA" />

<uses-permission
    android:name="android.permission.NEARBY_WIFI_DEVICES"
    android:usesPermissionFlags="neverForLocation" />

<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

The first two involve user/system interaction; `INTERNET` and `ACCESS_NETWORK_STATE` are automatically granted normal permissions. ([Android Developers][5])

**One important design choice:** if your goal is specifically *"scan a Wi-Fi QR code and make this Wi-Fi become one of the phone's saved networks"*, `ACTION_WIFI_ADD_NETWORKS` is probably the API you should investigate first, rather than trying to directly manipulate `WifiManager`. It is explicitly intended for adding saved networks with user approval. ([Android Developers][3])

[1]: https://developer.android.com/develop/connectivity/wifi/wifi-permissions?utm_source=chatgpt.com "Request permission to access nearby Wi-Fi devices  |  Connectivity  |  Android Developers"
[2]: https://developer.android.com/develop/connectivity/wifi/wifi-scan?utm_source=chatgpt.com "Wi-Fi scanning overview  |  Connectivity  |  Android Developers"
[3]: https://developer.android.com/develop/connectivity/wifi/wifi-save-network-passpoint-config?authuser=0000&utm_source=chatgpt.com "Save networks and Passpoint configurations  |  Connectivity  |  Android Developers"
[4]: https://developer.android.com/develop/connectivity/wifi/wifi-suggest?authuser=5&utm_source=chatgpt.com "Wi-Fi suggestion API for internet connectivity  |  Connectivity  |  Android Developers"
[5]: https://developer.android.com/develop/connectivity/network-ops/connecting?hl=pt-br&utm_source=chatgpt.com "Conectar à rede  |  Connectivity  |  Android Developers"

--------------------------------------------------------------------------------

explique o termo: Accessing nearby Wi-Fi
------------------------------


**“Accessing nearby Wi-Fi”** significa, em termos simples, **permitir que o aplicativo obtenha informações sobre redes Wi-Fi que estão próximas ao telefone**.

Não significa necessariamente "usar a Internet".

### Exemplo

Imagine que o telefone esteja em uma casa onde existem estas redes:

```text
📱 Telefone

Redes Wi-Fi próximas:
├── Casa_123       🔒
├── MeuWiFi        🔒
├── Cafe_Free      🔓
└── Neighbor_5G    🔒
```

Um aplicativo que **acessa redes Wi-Fi próximas** pode, dependendo da API e das permissões, descobrir informações como:

* quais redes Wi-Fi estão ao alcance;
* SSID/nome da rede;
* BSSID/identificador do ponto de acesso;
* intensidade do sinal;
* frequência/banda;
* informações sobre a conexão Wi-Fi atual.

Isso é diferente de simplesmente acessar a Internet.

### No seu aplicativo

Há uma diferença importante entre estas três situações:

**1. Usar a Internet**

```text
App → Internet → seu servidor
```

Precisa de:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Não exige diálogo ao usuário.

---

**2. Descobrir redes Wi-Fi próximas**

```text
App
 │
 ▼
📱 Wi-Fi
 │
 ├── Casa
 ├── Cafe
 └── Office
```

Aqui entra o conceito de **Nearby Wi-Fi devices** e as permissões correspondentes.

---

**3. Conectar a uma rede conhecida**

Por exemplo:

```text
QR Code
   │
   ▼
SSID = MyWiFi
Password = 12345678
   │
   ▼
Android Wi-Fi API
   │
   ▼
MyWiFi
```

Nesse caso, **você não necessariamente precisa descobrir as redes próximas**.

Se o QR Code já contém:

```text
WIFI:T:WPA;S:MyWiFi;P:12345678;;
```

o aplicativo já sabe:

* SSID = `MyWiFi`
* segurança = WPA
* senha = `12345678`

Portanto, não há necessidade de fazer um *Wi-Fi scan* para descobrir `MyWiFi`.

### Por que Android separa isso?

Porque **descobrir quais redes estão fisicamente próximas pode revelar informação sobre a localização do usuário**.

Por exemplo, se um aplicativo consegue observar:

```text
WiFi_A
WiFi_B
WiFi_C
WiFi_D
```

essa combinação pode ser usada para inferir aproximadamente onde o telefone está. Por isso Android passou a aplicar controles de privacidade específicos para operações de Wi-Fi.

### Para o seu caso

Se a ideia é:

> **"O usuário aponta a câmera para um QR Code de Wi-Fi e o aplicativo conecta o telefone àquela rede."**

eu separaria conceitualmente:

```text
QR Code
   │
   ├── Camera permission
   │
   ▼
SSID + credentials
   │
   ▼
Wi-Fi connection API
   │
   ▼
Android asks/controls connection
   │
   ▼
Connected
```

Você **não precisa necessariamente de “Nearby Wi-Fi” apenas para ler o SSID do QR Code**.

A permissão *Nearby Wi-Fi* torna-se relevante quando o aplicativo precisa **interagir com ou descobrir dispositivos/redes Wi-Fi próximas**, e não simplesmente interpretar uma informação que veio de um QR Code.

--------------------------------------------------------------------------------

poderia dar exemplos de como esses dados são armazenados, tipo, tamanho e exemplos reais:
	SSID/nome da rede; 
	BSSID/identificador do ponto de acesso;
------------------------------


Sim. Esses dois campos são bem diferentes em tamanho e finalidade.

### 1. SSID — nome da rede Wi-Fi

O **SSID (Service Set Identifier)** é o nome que você normalmente vê na lista de Wi-Fi do celular.

Exemplos reais:

```text
MinhaCasa
NET_2G4_8A31
VIVOFIBRA-5G
Cafe Central
TP-Link_4F20
```

Tecnicamente, o SSID é uma sequência de **0 a 32 bytes**. Quando representa texto ASCII simples, isso normalmente equivale a até **32 caracteres**.

Exemplo:

```text
SSID = "MinhaCasa"
```

Em bytes ASCII:

```text
M  i  n  h  a  C  a  s  a
77 105 110 104 97 67 97 115 97
```

→ **9 bytes**

Um SSID pode também conter caracteres Unicode, como:

```text
"João's Wi-Fi"
"Casa São Paulo"
"☕ Cafe WiFi"
```

Nesse caso, o número de bytes pode ser maior que o número de caracteres.

---

### 2. BSSID — identificador do ponto de acesso

O **BSSID (Basic Service Set Identifier)** identifica uma interface/ponto de acesso Wi-Fi específico.

Normalmente ele aparece como um endereço MAC de **48 bits = 6 bytes**:

```text
BSSID = 84:16:F9:2A:7C:01
```

Outro exemplo:

```text
BSSID = A4:2B:B0:13:9F:7D
```

Portanto, em uma estrutura binária, você pode pensar simplesmente:

```text
BSSID
└── 6 bytes
```

Quando armazenado como texto, normalmente ocupa:

```text
"84:16:F9:2A:7C:01"
```

**17 caracteres** contando os cinco `:`.

---

### SSID vs BSSID

Uma analogia simples:

```text
SSID  = nome do estabelecimento
BSSID = endereço específico da filial
```

Por exemplo:

```text
SSID:  Starbucks
       │
       ├── BSSID: AA:BB:CC:11:22:01
       ├── BSSID: AA:BB:CC:11:22:02
       └── BSSID: AA:BB:CC:11:22:03
```

Você pode ter **vários BSSIDs associados ao mesmo SSID**.

Isso é muito comum em uma rede doméstica moderna:

```text
                    "MinhaCasa"
                         │
              ┌──────────┴──────────┐
              │                     │
         2.4 GHz AP             5 GHz AP
              │                     │
     AA:BB:CC:11:22:01      AA:BB:CC:11:22:02
```

O usuário vê apenas:

```text
MinhaCasa
```

mas o telefone pode identificar diferentes pontos de acesso/BSSIDs.

### Como isso poderia ficar em um banco de dados

Para um aplicativo que registra redes descobertas, você poderia ter algo como:

```text
wifi_network
--------------------------------
id          INTEGER
ssid        TEXT
bssid       TEXT
frequency   INTEGER
rssi        INTEGER
security    TEXT
```

Exemplo:

```text
id:         42
ssid:       "MinhaCasa"
bssid:      "84:16:F9:2A:7C:01"
frequency:  5180
rssi:       -47
security:   "WPA2"
```

Se você estiver desenvolvendo um app Android que **apenas recebe SSID e senha através de QR Code**, provavelmente não precisa armazenar BSSID. O QR Code normalmente contém informações da **rede (SSID/segurança/senha)**, enquanto o BSSID identifica o **ponto de acesso físico específico**.

--------------------------------------------------------------------------------

como poderiam ser obtidos esses dados do exemplo, todos os 6 campos da tabela wifi-network? Existem outras informações ou dados que o phone fornece para redes wi-fi que eu também poderia armazenar nessa tabela? O id é apenas uma primary key sequential?
------------------------------


Sim. E há uma distinção importante: **alguns desses dados vêm diretamente de `WifiInfo`; outros vêm de `NetworkCapabilities`/`LinkProperties`; e alguns exigem um scan de redes próximas**.

### Os 6 campos do exemplo

| Campo       | De onde obter             | Exemplo             |
| ----------- | ------------------------- | ------------------- |
| `id`        | Seu banco de dados        | `42`                |
| `ssid`      | `WifiInfo`                | `MinhaCasa`         |
| `bssid`     | `WifiInfo`                | `84:16:F9:2A:7C:01` |
| `frequency` | `WifiInfo`                | `5180` MHz          |
| `rssi`      | `WifiInfo`                | `-47` dBm           |
| `security`  | configuração/scan da rede | `WPA2`              |

Para a **rede atualmente conectada**, a abordagem moderna no Android é obter `WifiInfo` através de `NetworkCallback.onCapabilitiesChanged()`. O Android 12 descontinuou `WifiManager.getConnectionInfo()` para esse propósito. ([Android Developers][1])

Conceitualmente:

```text
ConnectivityManager
        │
        ▼
NetworkCallback
        │
        ▼
NetworkCapabilities
        │
        ▼
WifiInfo
   ├── SSID
   ├── BSSID
   ├── RSSI
   ├── Frequency
   └── ...
```

### O que eu adicionaria à tabela

Se o objetivo do seu aplicativo é manter um **histórico das redes Wi-Fi que o telefone encontrou/usou**, eu consideraria algo assim:

```text
wifi_network
────────────────────────────────────────
id
ssid
bssid
frequency
rssi
security
link_speed
rx_link_speed
tx_link_speed
standard
ip_address
gateway
dns_servers
interface_name
is_metered
has_internet
first_seen
last_seen
```

Mas esses campos não têm todos a mesma natureza.

#### Dados da conexão Wi-Fi

`WifiInfo` pode fornecer informações como:

```text
SSID
BSSID
RSSI
frequency
link speed
TX link speed
RX link speed
Wi-Fi standard
```

Por exemplo:

```text
ssid:          "MinhaCasa"
bssid:         "84:16:F9:2A:7C:01"
frequency:     5180
rssi:          -47
link_speed:    866 Mbps
rx_link_speed: 866 Mbps
tx_link_speed: 866 Mbps
standard:      Wi-Fi 5
```

#### Dados da rede IP

Depois que você tem o objeto `Network`, pode obter `LinkProperties`. Ele fornece, entre outras coisas, **endereços IP locais, rotas, interface de rede e servidores DNS**. ([Android Developers][2])

Por exemplo:

```text
ip_address:     192.168.1.27
gateway:        192.168.1.1
dns_servers:
    192.168.1.1
    8.8.8.8

interface_name: wlan0
```

Esses dados são diferentes do BSSID: o BSSID identifica o ponto de acesso Wi-Fi, enquanto `192.168.1.27` identifica o endereço IP do telefone **dentro daquela rede naquele momento**.

### Dados sobre capacidade da conexão

`NetworkCapabilities` também permite verificar coisas como:

```text
TRANSPORT_WIFI
NET_CAPABILITY_INTERNET
NET_CAPABILITY_VALIDATED
NET_CAPABILITY_NOT_METERED
```

Um detalhe importante é que `NET_CAPABILITY_INTERNET` significa que a rede está configurada para oferecer Internet; **não garante que naquele momento ela realmente consiga alcançar a Internet**. Por exemplo, pode existir um captive portal. ([Android Developers][3])

Para seu aplicativo de teste de rede, isso é particularmente útil.

Você poderia registrar:

```text
has_internet:     true
validated:        true
metered:          false
```

### E o `id`?

Sim. Se você estiver usando SQLite/Room, o mais simples é:

```sql
id INTEGER PRIMARY KEY AUTOINCREMENT
```

Então:

```text
1
2
3
4
5
...
```

Mas eu **não usaria `id` para identificar a rede Wi-Fi**.

Por exemplo:

```text
id   ssid        bssid
1    MinhaCasa   AA:BB:CC:11:22:01
2    MinhaCasa   AA:BB:CC:11:22:02
3    Cafe        10:20:30:40:50:60
```

O `id` é apenas o identificador interno do registro.

Para identificar o ponto de acesso, o par:

```text
SSID + BSSID
```

é muito mais significativo.

### Uma arquitetura ainda melhor

Se seu objetivo é **histórico de redes**, eu separaria "rede" de "observação da rede":

```text
wifi_network
────────────────────
id
ssid
bssid
security
first_seen


wifi_observation
────────────────────
id
wifi_network_id
timestamp
rssi
frequency
link_speed
rx_link_speed
tx_link_speed
ip_address
...
```

Assim, uma mesma rede pode ser observada várias vezes:

```text
wifi_network
    │
    └── MinhaCasa / AA:BB:CC:11:22:01
             │
             ├── observation 1 → -72 dBm
             ├── observation 2 → -58 dBm
             ├── observation 3 → -47 dBm
             └── observation 4 → -51 dBm
```

Isso é muito mais interessante para um **network-test app**, porque você consegue analisar como a qualidade da conexão mudou ao longo do tempo, em vez de sobrescrever o valor anterior.

Também vale observar que **descobrir redes próximas** e obter informações da rede atualmente conectada são operações diferentes no Android. APIs de scan, como `getScanResults()`, têm requisitos adicionais de permissão/privacidade; não é necessário fazer um scan simplesmente para obter informações da conexão Wi-Fi atual. ([Android Developers][4])

[1]: https://developer.android.com/about/versions/12/behavior-changes-12?utm_source=chatgpt.com "Behavior changes: Apps targeting Android 12  |  Android Developers"
[2]: https://developer.android.com/develop/connectivity/network-ops/reading-network-state?authuser=9&hl=pt-br&utm_source=chatgpt.com "Ler o estado da rede  |  Connectivity  |  Android Developers"
[3]: https://developer.android.com/develop/connectivity/network-ops/reading-network-state?authuser=4&utm_source=chatgpt.com "Read network state  |  Connectivity  |  Android Developers"
[4]: https://developer.android.com/about/versions/10/privacy/changes?utm_source=chatgpt.com "Privacy changes in Android 10  |  Android Developers"

--------------------------------------------------------------------------------

explique todos esses campos da tabela wifi_network: 
bssid frequency rssi security link_speed rx_link_speed tx_link_speed standard ip_address gateway dns_servers interface_name is_metered has_internet first_seen last_seen
------------------------------


Claro. A forma mais fácil de entender é separar os campos em **identificação da rede**, **qualidade do Wi-Fi**, **rede IP**, **estado da Internet** e **histórico**.

## 1. Identificação do Wi-Fi

### `bssid`

Identificador do **ponto de acesso Wi-Fi específico**.

Exemplo:

```text
84:16:F9:2A:7C:01
```

É parecido com um endereço MAC e normalmente possui 6 bytes.

Uma mesma rede (`SSID`) pode ter vários BSSIDs:

```text
SSID = MinhaCasa

BSSID 1 = 84:16:F9:2A:7C:01  ← 2.4 GHz
BSSID 2 = 84:16:F9:2A:7C:02  ← 5 GHz
BSSID 3 = 84:16:F9:2A:7C:03  ← outro AP
```

**Serve para:** distinguir diferentes pontos de acesso que podem anunciar o mesmo SSID.

---

### `frequency`

Frequência de rádio utilizada pela conexão Wi-Fi, em **MHz**.

Exemplos:

```text
2412  → 2.4 GHz
5180  → 5 GHz
5955  → 6 GHz
```

Portanto:

```text
frequency = 5180
```

significa aproximadamente **5,18 GHz**.

**Serve para:** saber em qual canal/banda a conexão está operando.

---

## 2. Qualidade e velocidade do Wi-Fi

### `rssi`

**Received Signal Strength Indicator** — uma medida da intensidade do sinal recebido.

Normalmente aparece em **dBm**, com valores negativos:

```text
-35 dBm
-50 dBm
-65 dBm
-75 dBm
-85 dBm
```

Quanto mais próximo de zero, maior o sinal.

De forma simplificada:

```text
-40 dBm  → sinal muito forte
-55 dBm  → forte
-65 dBm  → razoável
-75 dBm  → fraco
-85 dBm  → muito fraco
```

É importante não interpretar RSSI como velocidade de Internet. Um sinal forte **não significa necessariamente Internet rápida**.

---

### `security`

Tipo de segurança/autenticação da rede.

Exemplos conceituais:

```text
OPEN
WPA2
WPA3
WPA2/WPA3
```

Por exemplo:

```text
SSID:     Cafe_Free
security: OPEN
```

ou:

```text
SSID:     MinhaCasa
security: WPA2
```

Para um aplicativo real, eu armazenaria isso de forma mais estruturada do que simplesmente uma string, porque Android pode apresentar diferentes combinações de segurança.

---

### `link_speed`

Velocidade do **link Wi-Fi** entre o telefone e o access point.

Exemplo:

```text
link_speed = 866 Mbps
```

Isso **não é a velocidade da Internet**.

Você poderia ter:

```text
Wi-Fi link:     866 Mbps
Internet:        94 Mbps
```

porque o roteador pode estar conectado à Internet através de um link de apenas 100 Mbps.

---

### `rx_link_speed`

**Receive link speed**.

É a velocidade estimada do link para **receber dados** no telefone.

Exemplo:

```text
rx_link_speed = 866 Mbps
```

Conceitualmente:

```text
Internet → Router → 📱
              866 Mbps
```

---

### `tx_link_speed`

**Transmit link speed**.

É a velocidade estimada do link para **enviar dados** do telefone para o access point.

Exemplo:

```text
tx_link_speed = 780 Mbps
```

Assim você pode ter:

```text
RX = 866 Mbps
TX = 780 Mbps
```

Isso pode acontecer porque as condições de transmissão e recepção não são necessariamente idênticas.

---

### `standard`

Padrão/geração do Wi-Fi utilizado.

Exemplos:

```text
Wi-Fi 4
Wi-Fi 5
Wi-Fi 6
Wi-Fi 6E
Wi-Fi 7
```

Correspondências:

```text
802.11n   → Wi-Fi 4
802.11ac  → Wi-Fi 5
802.11ax  → Wi-Fi 6 / 6E
802.11be  → Wi-Fi 7
```

No Android, `WifiInfo.getWifiStandard()` fornece essa informação.

---

# 3. Configuração da rede IP

Aqui começamos a sair do **Wi-Fi propriamente dito** e entrar na rede IP à qual o telefone está conectado.

### `ip_address`

Endereço IP atribuído ao telefone naquela rede.

Exemplo:

```text
192.168.1.27
```

Ou, em IPv6:

```text
2001:db8:1234::42
```

Um telefone pode ter **mais de um endereço IP simultaneamente**, especialmente quando IPv6 está habilitado.

Por isso, para um banco de dados sério, eu provavelmente **não colocaria apenas um `ip_address TEXT`**.

---

### `gateway`

O gateway padrão utilizado pelo telefone.

Em uma rede doméstica típica:

```text
Telefone
192.168.1.27
      │
      ▼
Router
192.168.1.1
      │
      ▼
Internet
```

Então:

```text
ip_address = 192.168.1.27
gateway    = 192.168.1.1
```

O gateway geralmente é o roteador que encaminha o tráfego para fora da rede local.

---

### `dns_servers`

Servidores DNS configurados para aquela conexão.

Exemplo:

```text
8.8.8.8
1.1.1.1
```

Ou o próprio roteador:

```text
192.168.1.1
```

O DNS transforma nomes como:

```text
example.com
```

em endereços IP.

Para o banco, eu evitaria armazenar isso como uma única string:

```text
"8.8.8.8,1.1.1.1"
```

e preferiria uma tabela relacionada:

```text
wifi_dns_server
----------------
id
wifi_network_id
address
```

porque uma conexão pode ter vários DNS.

---

### `interface_name`

Nome da interface de rede usada pelo telefone.

Normalmente algo como:

```text
wlan0
```

Exemplo:

```text
interface_name = wlan0
```

O significado é basicamente:

```text
Android
   │
   ├── wlan0   ← Wi-Fi
   ├── rmnet0  ← celular
   └── ...
```

É útil principalmente para diagnóstico técnico.

---

# 4. Estado da conexão com a Internet

### `is_metered`

Indica se o Android considera aquela conexão como **medida/limitada** (*metered*).

Por exemplo, uma rede celular geralmente é tratada como metered:

```text
Wi-Fi doméstico → false
4G/5G            → true
```

Mas um Wi-Fi também pode ser marcado como metered.

Isso é importante porque aplicativos podem evitar grandes downloads quando:

```text
is_metered = true
```

Para seu aplicativo de teste de velocidade, por exemplo, isso poderia ser usado para avisar que o teste poderá consumir uma quantidade significativa de dados.

---

### `has_internet`

Aqui há uma sutileza importante.

Você poderia pensar:

```text
has_internet = true
```

como:

> "Consigo acessar a Internet."

Mas isso depende de **como você obtém o valor**.

Android possui diferentes conceitos relacionados a:

```text
NET_CAPABILITY_INTERNET
NET_CAPABILITY_VALIDATED
```

Uma rede pode estar configurada para fornecer Internet, mas naquele momento não estar efetivamente validada.

Por exemplo:

```text
Wi-Fi conectado
       ↓
Router
       ↓
❌ Internet indisponível
```

Portanto, para seu aplicativo eu usaria algo mais preciso:

```text
has_internet
internet_validated
```

ou simplesmente:

```text
internet_status
```

com valores como:

```text
UNKNOWN
NO
YES
CAPTIVE_PORTAL
```

O Android fornece `NET_CAPABILITY_VALIDATED` justamente para indicar que o sistema conseguiu validar a conectividade com a Internet.

---

# 5. Histórico

### `first_seen`

Data/hora em que o aplicativo **viu aquela rede pela primeira vez**.

Exemplo:

```text
2026-09-22 08:15:32
```

Imagine:

```text
22/09 08:15 → primeira vez que apareceu
22/09 09:30 → novamente
23/09 08:10 → novamente
```

Então:

```text
first_seen = 2026-09-22 08:15:32
```

Esse campo normalmente **não deve ser alterado**.

---

### `last_seen`

Data/hora da **última observação** daquela rede.

No exemplo anterior:

```text
last_seen = 2026-09-23 08:10:17
```

Assim:

```text
first_seen = 2026-09-22 08:15:32
last_seen  = 2026-09-23 08:10:17
```

Isso permite saber há quanto tempo uma determinada rede é conhecida pelo aplicativo.

---

# Uma visão completa

Um registro poderia ser:

```text
wifi_network
──────────────────────────────────────────────
id:               42
ssid:             "MinhaCasa"
bssid:            "84:16:F9:2A:7C:01"

frequency:        5180
rssi:             -47
security:         "WPA2"

link_speed:       866
rx_link_speed:    866
tx_link_speed:    780
standard:         "Wi-Fi 5"

ip_address:       "192.168.1.27"
gateway:          "192.168.1.1"
dns_servers:      "192.168.1.1,8.8.8.8"
interface_name:   "wlan0"

is_metered:       false
has_internet:     true

first_seen:       "2026-09-22T08:15:32"
last_seen:        "2026-09-22T09:47:11"
```

Mas há uma questão de **modelagem de banco** importante aqui: eu não colocaria necessariamente tudo isso em `wifi_network`.

Alguns campos descrevem a **identidade da rede**:

```text
ssid
bssid
security
```

Outros descrevem uma **observação naquele instante**:

```text
rssi
frequency
link_speed
rx_link_speed
tx_link_speed
ip_address
gateway
dns_servers
is_metered
has_internet
```

Esses valores podem mudar constantemente.

Por exemplo:

```text
08:00 → RSSI -42
08:30 → RSSI -61
09:00 → RSSI -73
09:30 → RSSI -51
```

Se você colocar `rssi` diretamente em `wifi_network`, estará **perdendo o histórico**.

Para um aplicativo de network testing, eu modelaria mais provavelmente:

```text
                    wifi_network
                         │
                         │ 1:N
                         ▼
                  wifi_observation
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
             RSSI     speed       latency
```

Isso permite que o aplicativo tenha uma coisa muito mais útil: **o histórico da qualidade daquela conexão Wi-Fi ao longo do tempo**, em vez de apenas guardar o estado atual.

--------------------------------------------------------------------------------



--------------------------------------------------------------------------------
