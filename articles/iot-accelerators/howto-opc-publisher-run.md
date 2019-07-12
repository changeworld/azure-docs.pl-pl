---
title: Uruchom wydawca OPC — Azure | Dokumentacja firmy Microsoft
description: Uruchamianie wydawcy OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3b386171afc7916e5e803c39a9c7b3520752e6fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603748"
---
# <a name="run-opc-publisher"></a>Uruchamianie wydawcy OPC

W tym artykule opisano sposób uruchamiania debugowania ad wydawcy OPC. Dotyczy on również zagadnienia dotyczące wydajności i pamięci.

## <a name="command-line-options"></a>Opcje wiersza polecenia

Użycie aplikacji jest wyświetlana przy użyciu `--help` opcji wiersza polecenia w następujący sposób:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Zwykle określasz parametry połączenia Centrum IoT Hub właściciel, tylko przy pierwszym uruchomieniu aplikacji. Ciąg połączenia jest zaszyfrowane i przechowywane w magazynie certyfikatów platformy. Na nowsze działa aplikacja odczytuje parametry połączenia z magazynu certyfikatów. Jeśli określisz ciąg połączenia przy każdym uruchomieniu urządzenia, które jest tworzona dla aplikacji w rejestrze urządzeń usługi IoT Hub jest usunięta i utworzona ponownie.

## <a name="run-natively-on-windows"></a>Działa natywnie w Windows

Otwórz **opcpublisher.sln** projektu za pomocą programu Visual Studio, skompilować rozwiązanie i opublikować go. Możesz uruchomić aplikację w **katalog docelowy** zostały opublikowane w następujący sposób:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Użyj samodzielnie kontenera

Tworzenie własnego kontenera, a następnie uruchom go w następujący sposób:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Użyj kontenera z rejestru kontenerów firmy Microsoft

Brak dostępnej wbudowanych kontenerów w rejestrze kontenerów firmy Microsoft. Uruchom w następujący sposób:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Sprawdź [usługi Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) Aby wyświetlić obsługiwane systemy operacyjne i architektury procesora. Jeśli architektury systemu operacyjnego i procesora CPU jest obsługiwany, Docker automatycznie wybiera poprawny kontener.

## <a name="run-as-an-azure-iot-edge-module"></a>Uruchom jako moduł usługi Azure IoT Edge

Wydawca OPC są gotowe do użycia jako [usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) modułu. Korzystając z wydawcy OPC jako moduł usługi IoT Edge, obsługuje tylko protokoły transportu **Amqp_Tcp_Only** i **Mqtt_Tcp_Only**.

Aby dodać jako moduł wydawcy OPC wdrożenia usługi IoT Edge, przejdź do ustawień usługi IoT Hub w witrynie Azure portal i wykonaj następujące czynności:

1. Przejdź do **usługi IoT Edge** i Utwórz lub wybierz urządzenia usługi IoT Edge.
1. Wybierz pozycję **Ustaw moduły**.
1. Wybierz **Dodaj** w obszarze **moduły wdrożeń** i następnie **moduł usługi IoT Edge**.
1. W **nazwa** wprowadź **wydawcy**.
1. W **identyfikator URI obrazu** wprowadź `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Dostępne tagi można znaleźć na [usługi Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Wklej następujący kod JSON do **opcje tworzenia kontenera** pola:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Ta konfiguracja umożliwia skonfigurowanie usługi IoT Edge, aby uruchomić kontener o nazwie **wydawcy** przy użyciu obrazu wydawcy OPC. Nazwa hosta kontenera systemu jest ustawiona na **wydawcy**. Wydawca OPC jest wywoływana przy użyciu następującego argumentu wiersza polecenia: `--aa`. Po wybraniu tej opcji wydawca OPC zaufania certyfikatów serwerów OPC UA, z którą jest połączona. Można użyć opcji wiersza polecenia dowolnego wydawcy OPC. Jedynym ograniczeniem jest to rozmiar **opcje tworzenia kontenera** obsługiwane przez usługi IoT Edge.

1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.
1. Jeśli użytkownik chce przetwarzać dane wyjściowe z wydawcy OPC lokalnie za pomocą innego modułu usługi IoT Edge, wróć do **Ustaw moduły** strony. Następnie przejdź do **określanie tras** karta i Dodaj nową trasę, który wygląda podobnie do poniższej JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Ponownie **Ustaw moduły** wybierz opcję **dalej**, aż dojdziesz do ostatniej strony konfiguracji.
1. Wybierz **przesyłania** do wysyłania konfiguracji do usługi IoT Edge.
1. Po rozpoczęciu usługi IoT Edge na urządzeniu usługi edge i kontener platformy docker **wydawcy** działa, możesz zapoznać się dane wyjściowe dziennika wydawca OPC za pomocą `docker logs -f publisher` lub przez sprawdzanie pliku dziennika. W poprzednim przykładzie, plik dziennika jest powyżej `d:\iiotegde\publisher-publisher.log`. Można również użyć [narzędzie iot-edge — opc — wydawca diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Udostępnienie pliki konfiguracyjne na hoście

Aby usługi IoT Edge, konfiguracji modułu pliki dostępne w systemie plików hosta, należy użyć następującego **opcje tworzenia kontenera**. Poniższy przykład jest wdrożenia przy użyciu kontenerów systemu Linux dla Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Za pomocą tych opcji wydawca OPC odczytuje węzły, należy opublikować z pliku `./pn.json` i katalog roboczy kontenera jest równa `/appdata` przy uruchamianiu. Przy użyciu tych ustawień wydawca OPC odczytuje plik `/appdata/pn.json` z kontenera, który można pobrać swojej konfiguracji. Bez `--pf` opcji wydawca OPC próbuje odczytać domyślny plik konfiguracji `./publishednodes.json`.

Plik dziennika, używając nazwy domyślnej `publisher-publisher.log`, są zapisywane do `/appdata` i `CertificateStores` katalog jest tworzony także w tym katalogu.

Aby udostępnić te pliki w systemie plików hosta, konfigurację kontenera wymaga woluminu instalacji powiązania. `d://iiotedge:/appdata` Powiązania mapuje katalog `/appdata`, który jest bieżący katalog roboczy podczas uruchamiania kontenera, do katalogu hosta `d://iiotedge`. Bez tej opcji nie danych plików są utrwalane podczas następnego uruchomienia kontenera.

Jeśli korzystasz z kontenerów Windows, a następnie składnia `Binds` parametru jest inna. Katalog roboczy podczas uruchamiania kontenera jest `c:\appdata`. Aby umieścić ten plik konfiguracji w katalogu `d:\iiotedge`na hoście, należy określić następujące mapowanie w `HostConfig` sekcji:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Jeśli korzystasz z kontenerów systemu Linux w systemie Linux, składnia `Binds` parametru różni się ponownie. Katalog roboczy podczas uruchamiania kontenera jest `/appdata`. Aby umieścić ten plik konfiguracji w katalogu `/iiotedge` na hoście, należy określić następujące mapowanie w `HostConfig` sekcji:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Uwagi dotyczące korzystania z kontenera

W poniższych sekcjach wymieniono kilka rzeczy, o których należy pamiętać, korzystając z kontenera:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Dostęp do serwera OPC wydawcy OPC UA

Domyślnie serwer OPC wydawcy OPC UA nasłuchuje na porcie 62222. Aby udostępnić ten port wejściowy w kontenerze, użyj następującego polecenia:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Włączanie rozpoznawanie nazw intercontainer

Aby włączyć rozpoznawanie nazw z w ramach kontenera do innych kontenerów, należy utworzyć użytkownika zdefiniować siecią mostka platformy docker, a następnie nawiązać połączenie z kontenera tej sieci za pomocą funkcji `--network` opcji. Również przypisać kontenera przy użyciu nazwy `--name` opcji w następujący sposób:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontener jest teraz dostępny przy użyciu nazwy `publisher` przez innych kontenerów w tej samej sieci.

### <a name="access-other-systems-from-within-the-container"></a>Dostęp do innych systemów z w ramach kontenera

Inne kontenery można osiągnąć, korzystając z parametrów opisanych w poprzedniej sekcji. Jeśli system operacyjny, na którym jest hostowany platformy Docker jest system DNS włączony, następnie uzyskiwanie dostępu do wszystkich systemów, które są znane w systemie DNS działa.

W sieciach, które używają rozpoznawanie nazw systemu NetBIOS, należy włączyć dostęp do innych systemów za uruchamianie kontenera `--add-host` opcji. Ta opcja dodaje skutecznie wpis do pliku hosta kontenera:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Przypisywanie nazwy hosta

Wydawca OPC używa nazwy hosta maszyny, na którym jest uruchomiona potrzeby generowania certyfikatu i punktu końcowego. Docker wybiera losową nazwę hosta, jeśli jej nie ustawiono `-h` opcji. Poniższy przykład pokazuje, jak ustawić wewnętrzne nazwy hosta kontenera na `publisher`:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Użyj instalacji powiązania (udostępniony system plików)

Zamiast korzystać z systemu plików kontenera, możesz wybrać hosta systemu plików do przechowywania informacji o konfiguracji i plików dziennika. Aby skonfigurować tę opcję, należy użyć `-v` opcji `docker run` w trybie instalacji powiązania.

## <a name="opc-ua-x509-certificates"></a>Certyfikaty OPC UA X.509

OPC UA przy użyciu certyfikatów X.509 do uwierzytelniania klienta OPC UA i serwera, po nawiązaniu połączenia i do szyfrowania komunikacji między nimi. Wydawca OPC używa magazynów certyfikatów obsługiwana przez stosu OPC UA w celu zarządzania wszystkie certyfikaty. Podczas uruchamiania wydawca OPC sprawdza, czy certyfikat dla siebie. Jeśli występuje żaden certyfikat w magazynie certyfikatów i jeden nie jest jedną przekazanej w wierszu polecenia, wydawca OPC tworzy certyfikat z podpisem własnym. Aby uzyskać więcej informacji, zobacz **InitApplicationSecurityAsync** method in Class metoda `OpcApplicationConfigurationSecurity.cs`.

Certyfikaty z podpisem własnym nie zapewnia żadnych zabezpieczeń, ponieważ nie są one podpisane przez zaufanego urzędu certyfikacji.

Wydawca OPC udostępnia opcje wiersza polecenia do:

- Pobieranie informacji CSR posługują się wydawca OPC bieżącego certyfikatu aplikacji.
- Wydawca OPC aprowizacji przy użyciu urzędu certyfikacji certyfikat z podpisem.
- Wydawca OPC aprowizacji z nową parę kluczy i dopasowania urzędu certyfikacji certyfikat z podpisem.
- Dodawanie certyfikatów do zaufanego elementu równorzędnego lub magazynu certyfikatów zaufanych wystawców.
- Dodaj listę CRL.
- Usuwanie certyfikatu z zaufanego elementu równorzędnego lub magazynu certyfikatów zaufanych wystawców.

Te opcje umożliwiają przekazanie parametrów przy użyciu plików lub ciągi zakodowane w formacie base64.

Domyślny typ magazynu dla wszystkich magazynów certyfikatów to system plików, które można zmienić, używając opcji wiersza polecenia. Ponieważ kontener nie udostępnia pojemności magazynu trwałego w jej systemie plików, musisz wybrać typ innego magazynu. Użyj platformy Docker `-v` opcją trwałości certyfikat są przechowywane w systemie plików hosta lub w woluminie platformy Docker. Jeśli używasz woluminu platformy Docker, możesz przekazać certyfikatów przy użyciu ciągi zakodowane w formacie base64.

Środowisko uruchomieniowe ma wpływ na sposób certyfikaty są zachowywane. Należy unikać tworzenia nowych magazynów certyfikatów za każdym razem, gdy aplikacja zostanie uruchomiona:

- W Windows, działa w sposób macierzysty, nie można używać magazynu certyfikatów aplikacji typu `Directory` , ponieważ nie można uzyskać dostępu do klucza prywatnego. W tym przypadku opcja `--at X509Store`.
- Uruchomione jako kontener platformy docker w systemie Linux, możesz mapować magazynów certyfikatów do systemu plików hosta za pomocą platformy docker, uruchom opcję `-v <hostdirectory>:/appdata`. Ta opcja powoduje, że certyfikat jest trwały we wszystkich przebiegach aktualizacji aplikacji.
- Uruchamianie jako Linux kontener platformy docker i chcesz użyć X509 przechowywania certyfikatu aplikacji, korzystać z aparatu docker, uruchom opcję `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` i opcji aplikacji `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Zagadnienia dotyczące wydajności i pamięci

W tej sekcji omówiono opcje zarządzania pamięcią i wydajnością:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parametry wiersza polecenia do kontrolowania wydajności i pamięci

Po uruchomieniu wydawcy OPC, należy się pod uwagę wymagania dotyczące wydajności i zasobów pamięci dostępna na hoście.

Pamięć i wydajność mają współzależne i obie są zależne od konfiguracji liczbę węzłów, które można skonfigurować do publikowania. Upewnij się, że następujące parametry spełniają wymagania:

- Usługa IoT Hub wysyła interwału: `--si`
- Rozmiar komunikatu usługi IoT Hub (domyślny `1`): `--ms`
- Monitorowane elementy kolejki pojemność: `--mq`

`--mq` Parametr określa górną granicę pojemności kolejki wewnętrznej, który buforuje wszystkie powiadomienia zmiany wartości węzła OPC. Jeśli wydawca OPC nie mogą wysyłać komunikaty do usługi IoT Hub szybkie, buforów tej kolejki powiadomień. Parametr ustawia liczbę powiadomień, które mogą być buforowane. Jeśli liczba elementów w tej kolejce się przebiegów testowych, następnie w celu uniknięcia utraty wiadomości następujące czynności:

- Zmniejszanie czasu wysyłania usługi IoT Hub
- Zwiększ rozmiar komunikatów usługi IoT Hub

`--si` Parametr wymusza wydawcy OPC do wysyłania komunikatów do Centrum IoT Hub w określonym interwale. Wydawca OPC wysyła komunikat, jak określony rozmiar komunikatu przez `--ms` parametr osiągnięty, lub tak szybko, jak interwału określonego przez `--si` parametr zostanie osiągnięty. Aby wyłączyć opcję rozmiar wiadomości, użyj `--ms 0`. W takim przypadku wydawca OPC używa największy usługi IoT Hub komunikatów rozmiar wynoszący 256 kB do danych usługi batch.

`--ms` Umożliwia parametr batch komunikaty wysyłane do usługi IoT Hub. Protokół, którego używasz Określa, czy narzut na wysyłanie wiadomości do Centrum IoT Hub jest wysoka w porównaniu do rzeczywistego czasu wysyłania ładunku. W przypadku danego scenariusza daje opóźnienia w sytuacji, gdy dane są pozyskiwane przez usługę IoT Hub, należy skonfigurować wydawcy OPC do użycia największy rozmiar komunikatu 256 kB.

Przed użyciem wydawca OPC w scenariuszach produkcyjnych należy przetestować wydajność i użycie pamięci w warunkach produkcyjnych. Możesz użyć `--di` parametru, aby określić interwał w sekundach, wydawca OPC zapisuje informacje diagnostyczne.

### <a name="test-measurements"></a>Pomiary testu

Następujący przykład diagnostyki pokazują miar z różnymi wartościami dla `--si` i `--ms` parametry publikowania 500 węzłów OPC Interwał publikowania 1 sekundę.  Test używał wydawca OPC kompilacji debugowania w systemie Windows 10, natywnie przez 120 sekund. Protokół usługi IoT Hub był domyślnego protokołu MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Domyślna konfiguracja (--si 10 — ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Domyślna konfiguracja wysyła dane do usługi IoT Hub co 10 sekund lub gdy 256 kB danych jest dostępna dla usługi IoT Hub w celu pozyskiwania. Ta konfiguracja dodaje Średni czas oczekiwania około 10 sekund, ale ma najniższą prawdopodobieństwo utraty danych ze względu na rozmiar dużych bloków komunikatów. Dane wyjściowe diagnostyki pokazują, brak utraty aktualizacji węzła OPC: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Interwał wysyłania stałych (--si 1 ms — 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Jeśli rozmiar komunikatu wynosi 0 następnie wydawca OPC wewnętrznie partii danych za pomocą największej obsługiwane usługi IoT Hub rozmiar komunikatu, niż 256 KB. Diagnostyczne dane wyjściowe pokazują, że rozmiar komunikatu średnie bajty 115,019. W tej konfiguracji wydawca OPC nie utracić dowolnego węzła OPC aktualizacji wartości, a w porównaniu do domyślnego ma mniejsze opóźnienie.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Wyślij każdej aktualizacji wartości węzła OPC (--si 0 — ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Ta konfiguracja wysyła dla każdej wartości węzła OPC zmienić komunikat do Centrum IoT Hub. Pokaż diagnostykę rozmiar komunikatu średnie jest 234 bajtów, czyli małe. Zaletą tej konfiguracji jest, że wydawca OPC nie dodaje żadnych opóźnień. Liczba utraty aktualizacji wartości węzła OPC (`monitored item notifications enqueue failure: 44624`) jest wysoka, udostępniają ta konfiguracja nie nadaje się dla scenariuszy z dużych ilości danych telemetrycznych do opublikowania.

### <a name="maximum-batching---si-0---ms-262144"></a>Przetwarzanie wsadowe maksymalną (--si 0 — ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Tę konfigurację partii tyle aktualizacji wartości węzła OPC, jak to możliwe. Maksymalny rozmiar wiadomości usługi IoT Hub to 256 kB, który jest skonfigurowany w tym miejscu. Brak Brak interwału Wyślij żądanie, co oznacza, że ilość danych dla Centrum IoT Hub w celu pozyskiwania określa czas oczekiwania. Ta konfiguracja ma co najmniej prawdopodobieństwo utraty żadnych wartości węzła OPC i nadaje się do publikowania dużej liczby węzłów. Podczas używania tej konfiguracji upewnij się, że scenariusza nie ma warunków, gdzie jest wprowadzane duże opóźnienie, jeśli rozmiar komunikatu 256 kB nie zostanie osiągnięty.

## <a name="debug-the-application"></a>Debugowanie aplikacji

Aby debugować aplikację, otwórz **opcpublisher.sln** rozwiązanie plików za pomocą programu Visual Studio i używać programu Visual Studio, narzędzia debugowania.

Jeśli potrzebujesz dostępu do serwera OPC UA w wydawca OPC, upewnij się, że zapora zezwala na dostęp do portu, którego nasłuchuje serwer. Domyślny port to: 62222.

## <a name="control-the-application-remotely"></a>Zdalne sterowanie aplikacji

Konfigurowanie węzłów do opublikowania może odbywać się za pomocą usługi IoT Hub metody bezpośrednie.

Wydawca OPC implementuje kilka dodatkowych połączeń usługi IoT Hub metody bezpośredniej do odczytu:

- Informacje ogólne.
- Informacje diagnostyczne na sesje, subskrypcji i monitorowane elementy OPC.
- Informacje diagnostyczne dotyczące komunikatów usługi IoT Hub i zdarzenia.
- Dziennik uruchamiania.
- 100 ostatnich wierszy dziennika.
- Zamykanie aplikacji.

Następujące repozytoria GitHub zawiera narzędzia umożliwiające [konfigurowanie węzłów do publikowania](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) i [przeczytaj informacje diagnostyczne](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Oba narzędzia są także dostępne jako kontenery w usłudze Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Użyj przykładu serwera OPC UA

Jeśli nie masz prawdziwy serwer OPC UA, możesz użyć [przykładowy OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) na rozpoczęcie pracy. W tym przykładzie PLC jest również dostępna w usłudze Docker Hub.

Implementuje liczbę znaczników, które wygenerowania losowych danych i tagi z anomalii. Przykład można rozszerzyć, aby symulować wartości tagów dodatkowe.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wyjaśniono sposób uruchamiania wydawca OPC, zalecane następne kroki dotyczą Dowiedz się więcej o [bliźniaczej reprezentacji OPC](overview-opc-twin.md) i [magazynu OPC](overview-opc-vault.md).
