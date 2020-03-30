---
title: Konfigurowanie środowiska przejściowego w usłudze Azure Spring Cloud | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z wdrożenia w niebieskiej zieleni w usłudze Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471034"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurowanie środowiska przejściowego w usłudze Azure Spring Cloud

W tym artykule omówiono sposób konfigurowania wdrożenia przemieszczania przy użyciu wzorca wdrażania niebiesko-zielonego w usłudze Azure Spring Cloud. Wdrażanie niebieskie/zielone to wzorzec ciągłego dostarczania usługi Azure DevOps, który polega na utrzymywaniu działającej istniejącej wersji (niebieska) podczas wdrażania nowej (zielona). W tym artykule pokazano, jak umieścić to wdrożenie przejściowe w środowiskach produkcyjnych bez konieczności bezpośredniej zmiany wdrożenia produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że aplikacja PiggyMetrics została już wdrożona z naszego [samouczka na temat uruchamiania aplikacji usługi Azure Spring Cloud.](spring-cloud-quickstart-launch-app-portal.md) PiggyMetrics składa się z trzech aplikacji: "brama", "usługa konta" i "usługa auth".  

Jeśli chcesz użyć innej aplikacji w tym przykładzie, należy wprowadzić prostą zmianę w części publicznej aplikacji.  Ta zmiana odróżnia wdrożenie przejściowe od produkcji.

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, której można użyć do uruchomienia instrukcji w tym artykule.  Ma typowe, preinstalowane narzędzia platformy Azure, w tym najnowsze wersje git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli zalogujesz się do subskrypcji platformy Azure, uruchom usługę [Azure Cloud Shell.](https://shell.azure.com)  Aby dowiedzieć się więcej, zobacz [Omówienie usługi Azure Cloud Shell](../cloud-shell/overview.md).

Aby skonfigurować środowisko przejściowe w usłudze Azure Spring Cloud, postępuj zgodnie z instrukcjami w następnych sekcjach.

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie usługi Azure Spring Cloud dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Wyświetlanie wszystkich wdrożeń

Przejdź do wystąpienia usługi w witrynie Azure portal i wybierz **zarządzanie wdrażaniem,** aby wyświetlić wszystkie wdrożenia. Aby wyświetlić więcej szczegółów, można wybrać każde wdrożenie.

## <a name="create-a-staging-deployment"></a>Tworzenie wdrożenia przemieszczania

1. W lokalnym środowisku programistycznym należy wprowadzić niewielką modyfikację aplikacji bramy PiggyMetrics. Na przykład zmień kolor w pliku *gateway/src/main/resources/static/css/launch.css.* Dzięki temu można łatwo odróżnić dwa wdrożenia. Aby utworzyć pakiet jar, uruchom następujące polecenie: 

    ```console
    mvn clean package
    ```

1. W interfejsie wiersza polecenia platformy Azure utwórz nowe wdrożenie i nadaj mu nazwę wdrożenia przejściowego "zielona".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Po pomyślnym zakończeniu wdrażania należy uzyskać dostęp do strony bramy z **pulpitu nawigacyjnego aplikacji**i wyświetlić wszystkie wystąpienia na karcie **Wystąpienia aplikacji** po lewej stronie.
  
> [!NOTE]
> Stan odnajdowania jest *OUT_OF_SERVICE,* dzięki czemu ruch nie zostanie przekierowany do tego wdrożenia przed zakończeniem weryfikacji.

## <a name="verify-the-staging-deployment"></a>Weryfikowanie wdrożenia przemieszczania

1. Wróć do strony **Zarządzanie wdrażaniem** i wybierz nowe wdrożenie. Stan wdrożenia powinien być pokazywalny *Uruchomiony*. Przycisk **Przypisz/Cozbogaż sobsewaj domenę** powinien być wyszarzony, ponieważ środowisko jest środowiskiem przejściowym.

1. W okienku **Przegląd** powinien zostać wyświetlony **punkt końcowy testu**. Skopiuj i wklej go do nowego okna przeglądarki, a nowa strona PiggyMetrics powinna być wyświetlana.

>[!TIP]
> * Upewnij się, że punkt końcowy testu kończy się ukośnikiem (/), aby upewnić się, że plik CSS jest poprawnie załadowany.  
> * Jeśli przeglądarka wymaga wprowadzenia danych logowania w celu wyświetlenia strony, użyj [dekodowania adresu URL,](https://www.urldecoder.org/) aby zdekodować testowy punkt końcowy. Dekodowanie adresu URL zwraca adres\<URL w formularzu\<"https:// nazwa\<użytkownika>: hasło>@ nazwa klastra>.test.azureapps.io/gateway/green".  Ten formularz służy do uzyskiwania dostępu do punktu końcowego.

>[!NOTE]    
> Ustawienia serwera konfiguracji mają zastosowanie zarówno do środowiska przejściowego, jak i środowiska produkcyjnego. Na przykład jeśli ustawisz`server.servlet.context-path`ścieżkę kontekstu ( ) dla bramy aplikacji na serwerze konfiguracyjnym jako *somepath,* ścieżka do zielonego wdrożenia zmieni się na "https://\<nazwa użytkownika>:\<hasło>@\<nazwa klastra>.test.azureapps.io/gateway/green/somepath/...".
 
 Jeśli odwiedzasz w tym momencie bramę aplikacji skierowaną do publicznej sieci, powinna zostać wyświetlona stara strona bez nowej zmiany.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Ustaw zielone wdrożenie jako środowisko produkcyjne

1. Po zweryfikowaniu zmian w środowisku przejściowym można wypchnąć ją do środowiska produkcyjnego. Wróć do **zarządzania wdrażaniem**i zaznacz pole wyboru aplikacji **bramy.**

2. Wybierz **pozycję Ustaw wdrożenie**.
3. Na liście **Wdrażanie produkcyjne** **wybierz**zielony , a następnie wybierz pozycję **Zastosuj**.
4. Przejdź do strony **Przegląd** aplikacji bramy. Jeśli domena jest już przypisana do aplikacji bramy, adres URL pojawi się w okienku **Przegląd.** Aby wyświetlić zmodyfikowaną stronę PiggyMetrics, wybierz adres URL i przejdź do witryny.

>[!NOTE]
> Po ustawieniu wdrożenia na zielono jako środowiska produkcyjnego poprzednie wdrożenie staje się wdrożeniem przejściowym.

## <a name="modify-the-staging-deployment"></a>Modyfikowanie wdrożenia przemieszczania

Jeśli nie jesteś zadowolony ze zmiany, można zmodyfikować kod aplikacji, utworzyć nowy pakiet jar i przekazać go do zielonego wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Usuwanie wdrożenia przemieszczania

Aby usunąć wdrożenie tymczasowe z portu platformy Azure, przejdź do strony wdrażania przemieszczania, a następnie wybierz przycisk **Usuń.**

Alternatywnie należy usunąć wdrożenie przejściowe z interfejsu wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
