---
title: Skonfiguruj serwer konfiguracji w chmurze Azure wiosennej | Microsoft Docs
description: W tym samouczku dowiesz się, jak skonfigurować serwer sieci w chmurze wiosny dla chmury wiosennej platformy Azure na Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038923"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Samouczek: Konfigurowanie serwera sieci w chmurze wiosny dla usługi

W tym samouczku przedstawiono sposób nawiązywania połączenia ze sprężynowym serwerem konfiguracji chmury z usługą Azure wiosną w chmurze.

Konfiguracja chmury wiosennej zapewnia obsługę serwera i klienta na potrzeby konfiguracji zewnętrznej w systemie rozproszonym. Serwer konfiguracji ma centralne miejsce do zarządzania właściwościami zewnętrznymi aplikacji we wszystkich środowiskach. Aby dowiedzieć się więcej, zapoznaj się z informacjami na temat [serwera konfiguracji chmury wiosennej](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Została już zainicjowana i uruchomiona usługa w chmurze Azure wiosną.  Wykonaj [ten przewodnik Szybki Start](spring-cloud-quickstart-launch-app-cli.md) , aby zainicjować obsługę administracyjną usługi w chmurze Azure wiosną i uruchomić ją.


## <a name="restriction"></a>Ograniczenie

W przypadku korzystania z __serwera konfiguracji__ z zapleczem usługi git istnieją pewne ograniczenia. Niektóre właściwości zostaną automatycznie dodane do środowiska aplikacji w celu uzyskania dostępu do __serwera konfiguracji__ i __odnajdowania usług__. W przypadku skonfigurowania tych właściwości również z plików **serwera konfiguracji** mogą wystąpić konflikty i nieoczekiwane zachowanie.  Właściwości obejmują: 

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

Chmura sprężynowa platformy Azure obsługuje usługi Azure DevOps, GitHub, GitLab i Bitbucket do przechowywania plików konfiguracji serwera.  Gdy repozytorium jest gotowe, wprowadź w nich poniższe instrukcje i umieść je w tym miejscu.

Ponadto niektóre konfigurowalne właściwości są dostępne tylko dla niektórych typów. W poniższych podsekcjach wymieniono właściwości dla każdego typu repozytorium.


### <a name="public-repository"></a>Repozytorium publiczne

W przypadku korzystania z repozytorium publicznego właściwości konfigurowalne będą bardziej ograniczone.

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania repozytorium publicznego `Git`.

> [!NOTE]
> Obecnie obsługiwane są tylko łączniki ("-") Konwencja nazewnictwa oddzielonych wyrazów. Na przykład można użyć `default-label`, ale nie `defaultLabel`.

| Właściwość        | Wymagane | Funkcja                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | @No__t-0 repozytorium `Git` używane jako zaplecze serwera konfiguracji należy uruchomić z `http://`, `https://`, `git@` lub `ssh://`. |
| `default-label` | `no`     | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name` lub `commit-id` repozytorium. |
| `search-paths`  | `no`     | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Prywatne repozytorium z uwierzytelnianiem SSH

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania repozytorium prywatnego `Git` z `Ssh`.

> [!NOTE]
> Obecnie obsługiwane są tylko łączniki ("-") Konwencja nazewnictwa oddzielonych wyrazów. Na przykład można użyć `default-label`, ale nie `defaultLabel`.

| Właściwość                   | Wymagane | Funkcja                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | @No__t-0 repozytorium `Git` używane jako zaplecze serwera konfiguracji należy uruchomić z `http://`, `https://`, `git@` lub `ssh://`. |
| `default-label`            | `no`     | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name` lub `commit-id` repozytorium. |
| `search-paths`             | `no`     | Tablica ciągu służąca do przeszukiwania podkatalogów repozytorium `Git`. |
| `private-key`              | `no`     | Klucz prywatny `Ssh` w celu uzyskania dostępu do repozytorium `Git` jest __wymagany__ , gdy `uri` została uruchomiona z `git@` lub `ssh://`. |
| `host-key`                 | `no`     | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Algorytm klucza hosta powinien mieć jedną z `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` i `ecdsa-sha2-nistp521`. Wymagane tylko wtedy, gdy istnieje `host-key`. |
| `strict-host-key-checking` | `no`     | Wskazuje, czy uruchomienie serwera konfiguracji nie powiedzie się, gdy zostanie wykorzystana wartość `host-key`, powinna być `true` (wartością domyślną) lub `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Prywatne repozytorium z uwierzytelnianiem podstawowym

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania prywatnego repozytorium git z uwierzytelnianiem podstawowym.

> [!NOTE]
> Obecnie obsługiwane są tylko łączniki ("-") Konwencja nazewnictwa oddzielonych wyrazów. Można na przykład użyć wartości `default-label`, ale nie `defaultLabel`.

| Właściwość        | Wymagane | Funkcja                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | @No__t-0 repozytorium `Git` używane jako zaplecze serwera konfiguracji należy uruchomić z `http://`, `https://`, `git@` lub `ssh://`. |
| `default-label` | `no`     | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name` lub `commit-id` repozytorium. |
| `search-paths`  | `no`     | Tablica ciągu służąca do przeszukiwania podkatalogów repozytorium `Git`. |
| `username`      | `no`     | @No__t-0 służy do uzyskiwania dostępu do serwera repozytorium `Git`, __wymagany__ `Git` serwer repozytorium obsługuje `Http Basic Authentication`. |
| `password`      | `no`     | Hasło używane do uzyskiwania dostępu do serwera repozytorium `Git` __wymaga__ obsługi serwera repozytorium `Git` `Http Basic Authentication`. |

> [!NOTE]
> Niektóre serwery repozytorium `Git`, takie jak GitHub, obsługują certyfikat "Personal-token" lub "token dostępu" jako hasło dla `HTTP Basic Authentication`. Tutaj możesz użyć tego rodzaju tokenu jako hasła, a token "Personal" lub "Access-token" nie wygaśnie. Jednak w przypadku serwerów repozytorium git, takich jak BitBucket i Azure DevOps, token wygaśnie w ciągu jednej lub dwóch godzin, co oznacza, że ta opcja będzie niewykonalna w przypadku korzystania z chmury Azure wiosennej.

### <a name="git-repositories-with-pattern"></a>Repozytoria Git ze wzorcem

Poniżej znajdują się wszystkie konfigurowalne właściwości używane do konfigurowania repozytoriów Git ze wzorcem.

> [!NOTE]
> Obecnie obsługiwane są tylko łączniki ("-") Konwencja nazewnictwa oddzielonych wyrazów. Można na przykład użyć wartości `default-label`, ale nie `defaultLabel`.

| Właściwość                           | Wymagane         | Funkcja                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Mapa składa się z ustawień repozytoriów `Git` o podaną nazwę. |
| `repos."uri"`                      | `yes` w `repos` | @No__t-0 repozytorium `Git` używane jako zaplecze serwera konfiguracji należy uruchomić z `http://`, `https://`, `git@` lub `ssh://`. |
| `repos."name"`                     | `yes` w `repos` | Nazwa identyfikująca jedno repozytorium `Git`, __wymagane__ tylko wtedy, gdy istnieje `repos`. Na przykład powyżej, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Tablica ciągu służąca do dopasowania nazwy aplikacji dla każdego wzorca przyjmuje format `{application}/{profile}` z symbolami wieloznacznymi. |
| `repos."default-label"`            | `no`             | Domyślna etykieta repozytorium `Git` powinna być `branch name`, `tag name` lub `commit-id` repozytorium. |
| `repos."search-paths` "             | `no`             | Tablica ciągu służąca do przeszukiwania podkatalogów repozytorium `Git`. |
| `repos."username"`                 | `no`             | @No__t-0 służy do uzyskiwania dostępu do serwera repozytorium `Git`, __wymagany__ `Git` serwer repozytorium obsługuje `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Hasło używane do uzyskiwania dostępu do serwera repozytorium `Git` __wymaga__ obsługi serwera repozytorium `Git` `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | Klucz prywatny `Ssh` w celu uzyskania dostępu do repozytorium `Git` jest __wymagany__ , gdy `uri` została uruchomiona z `git@` lub `ssh://`. |
| `repos."host-key"`                 | `no`             | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Algorytm klucza hosta powinien mieć jedną z `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` i `ecdsa-sha2-nistp521`. __Wymagane__ tylko wtedy, gdy istnieje `host-key`. |
| `repos."strict-host-key-checking"` | `no`             | Wskazuje, czy uruchomienie serwera konfiguracji nie powiedzie się, gdy zostanie wykorzystana wartość `host-key`, powinna być `true` (wartością domyślną) lub `false`. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Importuj plik `application.yml` z konfiguracji chmury wiosennej

Niektóre domyślne ustawienia serwera konfiguracji można zaimportować bezpośrednio z witryny sieci Web [konfiguracji chmury wiosnowej](https://spring.io/projects/spring-cloud-config) . Można to zrobić bezpośrednio z poziomu Azure Portal, więc nie trzeba wykonywać żadnych kroków teraz, aby przygotować pliki lub repozytorium konfiguracyjne.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Dołączanie repozytorium serwera konfiguracji do chmury Azure wiosennej

Teraz, gdy masz zapisane pliki konfiguracji w repozytorium, musisz połączyć z nim chmurę sieciową platformy Azure.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do strony **omówienia** chmury Azure wiosennej.

1. Przejdź do karty **serwer konfiguracji** w polu Nagłówek **ustawień** w menu po lewej stronie.

### <a name="public-repository"></a>Repozytorium publiczne

Jeśli repozytorium jest publiczne, po prostu kliknij przycisk **publiczny** i wklej adres URL.

### <a name="private-repository"></a>Repozytorium prywatne

Chmura sprężynowa platformy Azure obsługuje uwierzytelnianie SSH. Postępuj zgodnie z instrukcjami w Azure Portal, aby dodać klucz publiczny do repozytorium. Następnie upewnij się, że klucz prywatny został uwzględniony w pliku konfiguracji.

Kliknij przycisk **Zastosuj** , aby zakończyć konfigurowanie serwera konfiguracji.


## <a name="delete-your-app-configuration"></a>Usuwanie konfiguracji aplikacji

Po zapisaniu pliku konfiguracji przycisk **Usuń konfigurację aplikacji** zostanie wyświetlony na karcie **Konfiguracja** . Spowoduje to całkowite wymazanie istniejących ustawień. Należy to zrobić, jeśli chcesz połączyć serwer konfiguracji z innym źródłem, na przykład przechodząc z witryny GitHub do usługi Azure DevOps.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób włączania i konfigurowania serwera konfiguracji. Aby dowiedzieć się więcej na temat zarządzania aplikacją, przejdź do samouczka dotyczącego ręcznego skalowania aplikacji.

> [!div class="nextstepaction"]
> [Dowiedz się, jak ręcznie skalować aplikację w chmurze ze sprężyną na platformie Azure](spring-cloud-tutorial-scale-manual.md).

