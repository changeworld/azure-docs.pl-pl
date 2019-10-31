---
title: Skonfiguruj serwer konfiguracji w chmurze Azure wiosennej | Microsoft Docs
description: W tym samouczku dowiesz się, jak skonfigurować serwer sieci w chmurze wiosny dla chmury wiosennej platformy Azure na Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/18/2019
ms.openlocfilehash: 3a091c22f49ec31029a1808c10e675a4d0960fb4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177917"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Samouczek: Konfigurowanie serwera sieci w chmurze wiosny dla usługi

W tym samouczku przedstawiono sposób nawiązywania połączenia ze sprężynowym serwerem konfiguracji chmury z usługą Azure wiosną w chmurze.

Konfiguracja chmury wiosennej zapewnia obsługę serwera i klienta na potrzeby konfiguracji zewnętrznej w systemie rozproszonym. Serwer konfiguracji ma centralne miejsce do zarządzania właściwościami zewnętrznymi aplikacji we wszystkich środowiskach. Aby dowiedzieć się więcej, zapoznaj się z informacjami na temat [serwera konfiguracji chmury wiosennej](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Została już zainicjowana i uruchomiona usługa w chmurze Azure wiosną.  Wykonaj [ten przewodnik Szybki Start](spring-cloud-quickstart-launch-app-cli.md) , aby zainicjować obsługę administracyjną usługi w chmurze Azure wiosną i uruchomić ją.

## <a name="restriction"></a>Ograniczenie

W przypadku korzystania z __serwera konfiguracji__ z zapleczem usługi git istnieją pewne ograniczenia. Niektóre właściwości zostaną automatycznie dodane do środowiska aplikacji w celu uzyskania dostępu do __serwera konfiguracji__ i __odnajdowania usług__. W przypadku skonfigurowania tych właściwości również z plików **serwera konfiguracji** mogą wystąpić konflikty i nieoczekiwane zachowanie. Właściwości obejmują: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> Zdecydowanie zalecamy, aby __nie__ umieszczać powyższych właściwości w plikach aplikacji __serwera konfiguracji__ .

## <a name="create-your-config-server-files"></a>Tworzenie plików serwera konfiguracji

Chmura sprężynowa platformy Azure obsługuje usługi Azure DevOps, GitHub, GitLab i Bitbucket do przechowywania plików konfiguracji serwera. Gdy repozytorium jest gotowe, wprowadź w nich poniższe instrukcje i umieść je w tym miejscu.

Ponadto niektóre konfigurowalne właściwości są dostępne tylko dla niektórych typów. W poniższych podsekcjach wymieniono właściwości dla każdego typu repozytorium.

### <a name="public-repository"></a>Repozytorium publiczne

W przypadku korzystania z repozytorium publicznego właściwości konfigurowalne będą bardziej ograniczone.

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania repozytorium publicznego `Git`.

> [!NOTE]
> Używanie łącznika ("-") do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład można użyć `default-label`, ale nie `defaultLabel`.

| Właściwość        | Wymagane | Funkcja                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | `uri` repozytorium `Git` używany jako zaplecze serwera konfiguracji, należy uruchomić z `http://`, `https://`, `git@`lub `ssh://`. |
| `default-label` | `no`     | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name`lub `commit-id` repozytorium. |
| `search-paths`  | `no`     | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Prywatne repozytorium z uwierzytelnianiem SSH

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania repozytorium prywatnego `Git` z `Ssh`.

> [!NOTE]
> Używanie łącznika ("-") do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład można użyć `default-label`, ale nie `defaultLabel`.

| Właściwość                   | Wymagane | Funkcja                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | `uri` repozytorium `Git` używany jako zaplecze serwera konfiguracji, należy uruchomić z `http://`, `https://`, `git@`lub `ssh://`. |
| `default-label`            | `no`     | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name`lub `commit-id` repozytorium. |
| `search-paths`             | `no`     | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium `Git`. |
| `private-key`              | `no`     | `Ssh` klucz prywatny, aby uzyskać dostęp do repozytorium `Git`, __wymagane__ , gdy `uri` rozpoczyna się od `git@` lub `ssh://`. |
| `host-key`                 | `no`     | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Algorytm klucza hosta powinien mieć wartość `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`lub `ecdsa-sha2-nistp521`. Wymagane tylko wtedy, gdy istnieje `host-key`. |
| `strict-host-key-checking` | `no`     | Wskazuje, czy serwer konfiguracji nie zostanie uruchomiony podczas korzystania z prywatnego `host-key`. Powinna być `true` (wartość domyślna) lub `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Prywatne repozytorium z uwierzytelnianiem podstawowym

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania prywatnego repozytorium git z uwierzytelnianiem podstawowym.

> [!NOTE]
> Używanie łącznika ("-") do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład użyj `default-label` nie `defaultLabel`.

| Właściwość        | Wymagane | Funkcja                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | `uri` repozytorium `Git` używany jako zaplecze serwera konfiguracji, należy uruchomić z `http://`, `https://`, `git@`lub `ssh://`. |
| `default-label` | `no`     | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name`lub `commit-id` repozytorium. |
| `search-paths`  | `no`     | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium `Git`. |
| `username`      | `no`     | `username` używany do uzyskiwania dostępu do serwera repozytorium `Git`, __wymagany__ , gdy serwer repozytorium `Git` obsługuje `Http Basic Authentication`. |
| `password`      | `no`     | Hasło używane do uzyskiwania dostępu do serwera repozytorium `Git`, __wymagane__ , gdy serwer repozytorium `Git` obsługuje `Http Basic Authentication`. |

> [!NOTE]
> Niektóre serwery repozytorium `Git`, takie jak GitHub, obsługują certyfikat "Personal-token" lub "token dostępu" jako hasło dla `HTTP Basic Authentication`. Tutaj możesz użyć tego rodzaju tokenu jako hasła, a token "Personal" lub "Access-token" nie wygaśnie. Jednak w przypadku serwerów repozytorium git, takich jak BitBucket i Azure DevOps, token wygaśnie w ciągu jednej lub dwóch godzin, co oznacza, że ta opcja nie będzie zdolna do użycia z chmurą Azure wiosną.

### <a name="git-repositories-with-pattern"></a>Repozytoria Git ze wzorcem

Poniżej znajdują się wszystkie konfigurowalne właściwości używane do konfigurowania repozytoriów Git ze wzorcem.

> [!NOTE]
> Używanie łącznika ("-") do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład użyj `default-label` nie `defaultLabel`.

| Właściwość                           | Wymagane         | Funkcja                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Mapa składająca się z ustawień repozytorium `Git` o podaną nazwę. |
| `repos."uri"`                      | `yes` w `repos` | `uri` repozytorium `Git` używany jako zaplecze serwera konfiguracji, należy uruchomić z `http://`, `https://`, `git@`lub `ssh://`. |
| `repos."name"`                     | `yes` w `repos` | Nazwa identyfikująca jedno repozytorium `Git`, __wymagane__ tylko wtedy, gdy `repos` istnieje. Na przykład powyżej, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Tablica ciągów używanych do dopasowania nazwy aplikacji. Dla każdego wzorca Użyj formatu `{application}/{profile}` z symbolami wieloznacznymi. |
| `repos."default-label"`            | `no`             | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name`lub `commit-id` repozytorium. |
| `repos."search-paths` "             | `no`             | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium `Git`. |
| `repos."username"`                 | `no`             | `username` używany do uzyskiwania dostępu do serwera repozytorium `Git`, __wymagany__ , gdy serwer repozytorium `Git` obsługuje `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Hasło używane do uzyskiwania dostępu do serwera repozytorium `Git`, __wymagane__ , gdy serwer repozytorium `Git` obsługuje `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | `Ssh` klucz prywatny do uzyskiwania dostępu do repozytorium `Git`, __wymagany__ , gdy `uri` rozpoczyna się od `git@` lub `ssh://`. |
| `repos."host-key"`                 | `no`             | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Algorytm klucza hosta powinien mieć wartość `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`lub `ecdsa-sha2-nistp521`. __Wymagane__ tylko wtedy, gdy istnieje `host-key`. |
| `repos."strict-host-key-checking"` | `no`             | Wskazuje, czy serwer konfiguracji nie zostanie uruchomiony podczas korzystania z prywatnego `host-key`. Powinna być `true` (wartość domyślna) lub `false`. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Dołączanie repozytorium serwera konfiguracji do chmury Azure wiosennej

Teraz, gdy masz zapisane pliki konfiguracji w repozytorium, musisz połączyć z nim chmurę sieciową platformy Azure.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do strony **omówienia** chmury Azure wiosennej.

1. Przejdź do karty **serwer konfiguracji** w polu Nagłówek **ustawień** w menu po lewej stronie.

![zrzut ekranu okna](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Informacje o repozytorium wejściowym bezpośrednio do Azure Portal

#### <a name="default-repository"></a>Repozytorium domyślne

* Repozytorium publiczne: w sekcji **repozytorium domyślne** wklej identyfikator URI repozytorium w sekcji **Identyfikator URI** i upewnij się, że ustawienie **uwierzytelniania** jest **publiczne**. Następnie kliknij przycisk **Zastosuj** , aby zakończyć. 

* Repozytorium prywatne: chmura sprężynowa platformy Azure obsługuje podstawowe uwierzytelnianie oparte na hasłach i tokenach.

    * Uwierzytelnianie podstawowe: w sekcji **repozytorium domyślne** wklej identyfikator URI repozytorium w sekcji **Identyfikator URI** , a następnie kliknij pozycję **uwierzytelnianie**. Wybierz pozycję **podstawowa** jako **Typ uwierzytelniania** , a następnie wprowadź nazwę użytkownika i hasło/token, aby udzielić dostępu do chmury Azure wiosennej. Kliknij przycisk **OK** i **Zastosuj** , aby zakończyć konfigurowanie serwera konfiguracji.

    ![zrzut ekranu okna](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Niektóre serwery repozytorium git, takie jak GitHub, używają `personal-token` lub `access-token`, takich jak hasło **uwierzytelniania podstawowego**. Tego rodzaju tokenu można używać jako hasła w chmurze Azure wiosennej, ponieważ nigdy nie wygasa. Jednak w przypadku innych serwerów repozytorium git, takich jak BitBucket i Azure DevOps, `access-token` wygaśnie w ciągu jednej lub dwóch godzin. Oznacza to, że ta opcja nie jest przeznaczona do użycia w przypadku korzystania z tych serwerów repozytorium z chmurą usługi Azure wiosną.]

    * SSH: w sekcji **repozytorium domyślne** wklej identyfikator URI repozytorium w sekcji **Identyfikator URI** , a następnie kliknij pozycję **uwierzytelnianie**. Wybierz pozycję **SSH** jako **Typ uwierzytelniania** , a następnie wprowadź swój **klucz prywatny**. Opcjonalnie możesz określić **klucz hosta** i **algorytm klucza hosta**. Upewnij się, że klucz publiczny został uwzględniony w repozytorium serwera konfiguracji. Kliknij przycisk **OK** i **Zastosuj** , aby zakończyć konfigurowanie serwera konfiguracji.

    ![zrzut ekranu okna](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repozytorium wzorców

Jeśli chcesz użyć opcjonalnego **repozytorium wzorców** w celu skonfigurowania usługi, określ **Identyfikator URI** i **uwierzytelnianie** tak samo jak w przypadku **repozytorium domyślnego**. Upewnij się, że dołączono **nazwę** wzorca, a następnie kliknij przycisk **Zastosuj** , aby dołączyć go do wystąpienia. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Wprowadź informacje o repozytorium do pliku YAML

Jeśli zapisano plik YAML z ustawieniami repozytorium, możesz zaimportować plik YAML bezpośrednio z komputera lokalnego do chmury z systemem Azure wiosną. Prosty plik YAML dla repozytorium prywatnego z uwierzytelnianiem podstawowym będzie wyglądać następująco:

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

Kliknij przycisk **ustawienia importowania** , a następnie wybierz plik `.yml` z katalogu projektu. Kliknij pozycję **Importuj**, a następnie w oknie **powiadomienia** zostanie wyświetlona operacja `async`. Po 1-2 minutach powinna być zgłaszana sukces.

![zrzut ekranu okna](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Informacje zawarte w pliku YAML powinny być widoczne w Azure Portal. Kliknij przycisk **Zastosuj** , aby zakończyć. 


## <a name="delete-your-app-configuration"></a>Usuwanie konfiguracji aplikacji

Po zapisaniu pliku konfiguracji przycisk **Usuń konfigurację aplikacji** zostanie wyświetlony na karcie **Konfiguracja** . Spowoduje to całkowite wymazanie istniejących ustawień. Należy to zrobić, jeśli chcesz połączyć serwer konfiguracji z innym źródłem, na przykład przechodząc z witryny GitHub do usługi Azure DevOps.



## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób włączania i konfigurowania serwera konfiguracji. Aby dowiedzieć się więcej na temat zarządzania aplikacją, przejdź do samouczka dotyczącego ręcznego skalowania aplikacji.

> [!div class="nextstepaction"]
> [Dowiedz się, jak ręcznie skalować aplikację w chmurze ze sprężyną na platformie Azure](spring-cloud-tutorial-scale-manual.md).

