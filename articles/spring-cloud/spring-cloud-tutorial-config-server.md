---
title: Samouczek — konfigurowanie wystąpienia serwera konfiguracji w chmurze Azure Spring Cloud
description: W tym samouczku dowiesz się, jak skonfigurować wystąpienie serwera konfiguracji w chmurze Spring dla usługi Azure Spring Cloud w portalu Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277526"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Samouczek: Konfigurowanie wystąpienia serwera konfiguracji w chmurze spring dla usługi

W tym artykule pokazano, jak połączyć wystąpienie programu Spring Cloud Config Server z usługą Azure Spring Cloud.

Spring Cloud Config zapewnia obsługę serwera i klienta dla zewnętrznej konfiguracji w systemie rozproszonym. W wystąpieniu serwera config masz centralne miejsce do zarządzania właściwościami zewnętrznymi dla aplikacji we wszystkich środowiskach. Aby uzyskać więcej informacji, zobacz [Odwołanie do programu Spring Cloud Config Server](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 
* Już aprowizowana i uruchomiona usługa Azure Spring Cloud. Aby skonfigurować i uruchomić usługę Azure Spring Cloud, zobacz [Szybki start: Uruchamianie aplikacji Java Spring przy użyciu interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Ograniczenie

Istnieją pewne ograniczenia podczas korzystania z config server z git back end. Niektóre właściwości są automatycznie wtryskiwane do środowiska aplikacji, aby uzyskać dostęp do serwera konfiguracji i odnajdywania usług. Jeśli również skonfigurować te właściwości z plików config server, mogą wystąpić konflikty i nieoczekiwane zachowanie. Właściwości obejmują: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Zdecydowanie zaleca _się, aby nie_ umieszczać powyższych właściwości w plikach aplikacji serwera Config Server.

## <a name="create-your-config-server-files"></a>Tworzenie plików serwera config

Usługa Azure Spring Cloud obsługuje usługi Azure DevOps, GitHub, GitLab i Bitbucket do przechowywania plików serwera konfiguracji. Gdy repozytorium jest gotowe, utwórz pliki konfiguracyjne z następującymi instrukcjami i przechowuj je tam.

Ponadto niektóre konfigurowalne właściwości są dostępne tylko dla niektórych typów. Poniższe podsekcje wyświetlają właściwości dla każdego typu repozytorium.

### <a name="public-repository"></a>Repozytorium publiczne

Podczas korzystania z repozytorium publicznego, konfigurowalne właściwości są bardziej ograniczone.

Wszystkie konfigurowalne właściwości, które są używane do konfigurowania publicznego repozytorium Git są wymienione w poniższej tabeli:

> [!NOTE]
> Używanie łącznika (-) do oddzielnych wyrazów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład można użyć *domyślnej etykiety*, ale nie *defaultLabel*.

| Właściwość        | Wymagany | Funkcja                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Tak    | Identyfikator URI repozytorium Git, który jest używany jako zaplecze serwera konfiguracji, zaczyna się od *http://*, *https://* *, git@* lub *ssh://*. |
| `default-label` | Nie     | Domyślną etykietą repozytorium Git powinna być *nazwa gałęzi,* *nazwa znacznika*lub *identyfikator zatwierdzenia* repozytorium. |
| `search-paths`  | Nie     | Tablica ciągów, które są używane do wyszukiwania podkatalogów repozytorium Git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Prywatne repozytorium z uwierzytelnianiem SSH

Wszystkie konfigurowalne właściwości używane do konfigurowania prywatnego repozytorium Git z SSH są wymienione w poniższej tabeli:

> [!NOTE]
> Używanie łącznika (-) do oddzielnych wyrazów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład można użyć *domyślnej etykiety*, ale nie *defaultLabel*.

| Właściwość                   | Wymagany | Funkcja                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Tak    | Identyfikator URI repozytorium Git używanego jako zaplecze serwera konfiguracji należy uruchomić od *http://,* *https://* *, git@* lub *ssh://*. |
| `default-label`            | Nie     | Domyślną etykietą repozytorium Git powinna być *nazwa gałęzi,* *nazwa znacznika*lub *identyfikator zatwierdzenia* repozytorium. |
| `search-paths`             | Nie     | Tablica ciągów używanych do wyszukiwania podkatalogów repozytorium Git. |
| `private-key`              | Nie     | Klucz prywatny SSH, aby uzyskać dostęp do repozytorium Git, _wymagane,_ gdy identyfikator URI rozpoczyna się *od git@* lub *ssh://*. |
| `host-key`                 | Nie     | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty `host-key-algorithm`. |
| `host-key-algorithm`       | Nie     | Algorytm klucza hosta, powinien być *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, lub *ecdsa-sha2-nistp521*. *Wymagane* tylko `host-key` wtedy, gdy istnieje. |
| `strict-host-key-checking` | Nie     | Wskazuje, czy wystąpienie serwera config nie zostanie `host-key`uruchomiony podczas wykorzystaniem prywatnego . Powinna być *prawdziwa* (wartość domyślna) lub *fałsz*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Prywatne repozytorium z uwierzytelnianiem podstawowym

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania prywatnego repozytorium Git z uwierzytelnianiem podstawowym.

> [!NOTE]
> Używanie łącznika (-) do oddzielnych wyrazów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład użyj *domyślnej etykiety*, a nie *defaultLabel*.

| Właściwość        | Wymagany | Funkcja                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Tak    | Identyfikator URI repozytorium Git, który jest używany jako zapleczu serwera konfiguracji, powinien być uruchamiany z *http://*, *https://* *, git@* lub *ssh://*. |
| `default-label` | Nie     | Domyślną etykietą repozytorium Git powinna być *nazwa gałęzi,* *nazwa znacznika*lub *identyfikator zatwierdzenia* repozytorium. |
| `search-paths`  | Nie     | Tablica ciągów używanych do wyszukiwania podkatalogów repozytorium Git. |
| `username`      | Nie     | Nazwa użytkownika używana do uzyskiwania dostępu do serwera repozytorium Git _jest wymagana,_ gdy serwer repozytorium Git obsługuje `Http Basic Authentication`. |
| `password`      | Nie     | Hasło używane do uzyskiwania dostępu do serwera repozytorium Git, _wymagane,_ gdy serwer repozytorium Git obsługuje `Http Basic Authentication`. |

> [!NOTE]
> Wiele `Git` serwerów repozytorium obsługuje używanie tokenów, a nie haseł do uwierzytelniania podstawowego HTTP. Niektóre repozytoria, takie jak GitHub, umożliwiają tokeny do utrwalenia przez czas nieokreślony. Jednak niektóre serwery repozytorium Git, w tym Azure DevOps, wymuszają wygaśnięcie tokenów w ciągu kilku godzin. Repozytoria, które powodują wygaśnięcie tokenów, nie powinny używać uwierzytelniania opartego na tokenach w usłudze Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repozytoria Git ze wzorem

Wszystkie konfigurowalne właściwości używane do konfigurowania repozytoriów Git ze wzorcem są wymienione poniżej.

> [!NOTE]
> Używanie łącznika (-) do oddzielnych wyrazów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład użyj *domyślnej etykiety*, a nie *defaultLabel*.

| Właściwość                           | Wymagany         | Funkcja                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nie             | Mapa składająca się z ustawień repozytorium Git o podanej nazwie. |
| `repos."uri"`                      | Tak na`repos` | Identyfikator URI repozytorium Git, który jest używany jako zapleczu serwera konfiguracji, powinien być uruchamiany z *http://*, *https://* *, git@* lub *ssh://*. |
| `repos."name"`                     | Tak na`repos` | Nazwa do zidentyfikowania w repozytorium Git, _wymagana_ tylko wtedy, gdy `repos` istnieje. Na przykład *team-A*, *team-B*. |
| `repos."pattern"`                  | Nie             | Tablica ciągów używanych do dopasowania nazwy aplikacji. Dla każdego wzorca `{application}/{profile}` użyj formatu z symbolami wieloznaczymi. |
| `repos."default-label"`            | Nie             | Domyślną etykietą repozytorium Git powinna być *nazwa gałęzi,* *nazwa znacznika*lub *identyfikator zatwierdzenia* repozytorium. |
| `repos."search-paths`"             | Nie             | Tablica ciągów używanych do wyszukiwania podkatalogów repozytorium Git. |
| `repos."username"`                 | Nie             | Nazwa użytkownika używana do uzyskiwania dostępu do serwera repozytorium Git _jest wymagana,_ gdy serwer repozytorium Git obsługuje `Http Basic Authentication`. |
| `repos."password"`                 | Nie             | Hasło używane do uzyskiwania dostępu do serwera repozytorium Git, _wymagane,_ gdy serwer repozytorium Git obsługuje `Http Basic Authentication`. |
| `repos."private-key"`              | Nie             | Klucz prywatny SSH dostępu do repozytorium Git, _wymagany,_ gdy identyfikator URI zaczyna się *od git@* lub *ssh://*. |
| `repos."host-key"`                 | Nie             | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Nie             | Algorytm klucza hosta, powinien być *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, lub *ecdsa-sha2-nistp521*. *Wymagane* tylko `host-key` wtedy, gdy istnieje. |
| `repos."strict-host-key-checking"` | Nie             | Wskazuje, czy wystąpienie serwera config nie zostanie `host-key`uruchomiony podczas wykorzystaniem prywatnego . Powinna być *prawdziwa* (wartość domyślna) lub *fałsz*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Dołączanie repozytorium serwera config do usługi Azure Spring Cloud

Teraz, gdy pliki konfiguracyjne są zapisywane w repozytorium, należy połączyć z nim usługę Azure Spring Cloud.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Przejdź do strony **Przegląd chmury** usługi Azure Spring.

1. Wybierz usługę do skonfigurowania.

1. W lewym okienku strony usługi w obszarze **Ustawienia**wybierz kartę **Serwer konfiguracji.**

![Okno Serwer konfiguracji](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Wprowadzanie informacji o repozytorium bezpośrednio do witryny Azure portal

#### <a name="default-repository"></a>Domyślne repozytorium

* **Publiczne repozytorium:** W sekcji **Domyślne repozytorium** w polu **Uri** wklej identyfikator URI repozytorium.  Ustaw **etykietę** na **konfigura**. Upewnij się, że ustawienie **Uwierzytelnianie** jest **publiczne,** a następnie wybierz pozycję **Zastosuj,** aby zakończyć. 

* **Repozytorium prywatne:** Usługa Azure Spring Cloud obsługuje podstawowe uwierzytelnianie oparte na haśle/tokenie oraz protokół SSH.

    * **Uwierzytelnianie podstawowe:** W sekcji **Domyślne repozytorium** w polu **Uri** wklej identyfikator URI, a następnie wybierz przycisk **Uwierzytelnianie** (ikona ołówka). W okienku **Edytowanie uwierzytelniania** na liście rozwijanej **Typ uwierzytelniania** wybierz pozycję **HTTP Basic**, a następnie wprowadź nazwę użytkownika i hasło/token, aby udzielić dostępu do usługi Azure Spring Cloud. Wybierz **przycisk OK**, a następnie wybierz pozycję **Zastosuj,** aby zakończyć konfigurowanie wystąpienia serwera konfiguracji.

    ![Okienko Edytowanie uwierzytelniania](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Niektóre serwery repozytorium Git, takie jak GitHub, używają *tokenu osobistego* lub *tokenu dostępu,* takiego jak hasło, do **uwierzytelniania podstawowego.** Tego rodzaju tokenu można użyć jako hasła w usłudze Azure Spring Cloud, ponieważ nigdy nie wygaśnie. Jednak w przypadku innych serwerów repozytorium Git, takich jak Bitbucket i Azure DevOps, *token dostępu* wygasa w ciągu jednej lub dwóch godzin. Oznacza to, że opcja nie jest opłacalna, gdy używasz tych serwerów repozytorium z usługą Azure Spring Cloud.

    * **SSH:** W sekcji **Domyślne repozytorium** w polu **Uri** wklej identyfikator URI repozytorium, a następnie wybierz przycisk **Uwierzytelnianie** (ikona ołówka). W okienku **Edytowanie uwierzytelniania** na liście rozwijanej **Typ uwierzytelniania** wybierz pozycję **SSH**, a następnie wprowadź **klucz prywatny**. Opcjonalnie określ **klucz hosta** i **algorytm klucza hosta**. Pamiętaj, aby dołączyć klucz publiczny do repozytorium serwera config. Wybierz **przycisk OK**, a następnie wybierz pozycję **Zastosuj,** aby zakończyć konfigurowanie wystąpienia serwera konfiguracji.

    ![Okienko Edytowanie uwierzytelniania](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repozytorium wzorków

Jeśli chcesz użyć opcjonalnego **repozytorium wzorca** do skonfigurowania usługi, określ **identyfikator URI** i **uwierzytelnianie** w taki sam sposób, jak **repozytorium domyślne**. Pamiętaj, aby dołączyć **nazwę** wzorca, a następnie wybierz pozycję **Zastosuj,** aby dołączyć go do wystąpienia. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Wprowadzanie informacji o repozytorium do pliku YAML

Jeśli zapisano plik YAML z ustawieniami repozytorium, możesz zaimportować plik bezpośrednio z komputera lokalnego do usługi Azure Spring Cloud. Prosty plik YAML dla repozytorium prywatnego z uwierzytelnianiem podstawowym wyglądałby następująco:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Wybierz przycisk **Importuj ustawienia,** a następnie wybierz plik YAML z katalogu projektu. Wybierz **pozycję Importuj** `async` , a następnie pojawi się operacja z **powiadomieniami.** Po 1-2 minutach powinien zgłosić sukces.

![Okienko Powiadomienia serwera konfiguracji](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Informacje z pliku YAML powinny być wyświetlane w witrynie Azure portal. Wybierz **pozycję Zastosuj** do zakończenia. 


## <a name="delete-your-app-configuration"></a>Usuwanie konfiguracji aplikacji

Po zapisaniu pliku konfiguracyjnego na karcie **Konfiguracja** pojawi się przycisk **Usuń konfigurację aplikacji.** Należy go wybrać, jeśli chcesz połączyć wystąpienie serwera config z innym źródłem, na przykład przejście z github do usługi Azure DevOps.



## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak włączyć i skonfigurować wystąpienie serwera spring cloud config server. Aby dowiedzieć się więcej na temat zarządzania aplikacją, przejdź do samouczka na temat ręcznego skalowania aplikacji.

> [!div class="nextstepaction"]
> [Samouczek: Skalowanie aplikacji w chmurze azure spring cloud](spring-cloud-tutorial-scale-manual.md)
