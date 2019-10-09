---
title: Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak używać wdrożenia Blue-Green z chmurą Azure wiosennej
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038986"
---
# <a name="how-to-set-up-a-staging-environment"></a>Jak skonfigurować środowisko przejściowe

W tym artykule przedstawiono sposób użycia wdrożenia przemieszczania przy użyciu wzorca wdrażania Blue-Green w chmurze Azure wiosennej. Przedstawiono w nim również sposób umieszczania wdrożenia przemieszczania w środowisku produkcyjnym bez konieczności bezpośredniego zmiany wdrożenia produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że aplikacja PiggyMetrics została już wdrożona z naszego [samouczka dotyczącego uruchamiania aplikacji](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics składa się z trzech aplikacji: "Gateway", "Account-Service" i "auth-Service".  

Jeśli masz inną aplikację, której chcesz użyć do tego przykładu, musisz wprowadzić prostą zmianę w publicznej części aplikacji.  Ta zmiana odróżnia wdrożenie przejściowe od środowiska produkcyjnego.

>[!NOTE]
> Przed rozpoczęciem pracy z tym przewodnikiem Szybki Start upewnij się, że Twoja subskrypcja platformy Azure ma dostęp do chmury wiosennej platformy Azure.  Jako usługa w wersji zapoznawczej prosimy o skontaktowanie się z nami, aby można było dodać subskrypcję do listy dozwolonych.  Jeśli chcesz zapoznać się z możliwościami chmury wiosennej platformy Azure, skontaktuj się z nami za pośrednictwem poczty e-mail: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

Aby ukończyć ten artykuł:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
1. [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
1. [Zainstalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>Wyświetl wszystkie wdrożenia

Przejdź do wystąpienia usługi w Azure Portal a następnie wybierz pozycję **Zarządzanie wdrażaniem** , aby wyświetlić wszystkie wdrożenia. Aby uzyskać więcej informacji, możesz wybrać każde wdrożenie.

## <a name="create-a-staging-deployment"></a>Tworzenie wdrożenia przemieszczania

1. W lokalnym środowisku programistycznym wprowadź małą modyfikację aplikacji bramy Piggy Metric. Na przykład zmień kolor w `gateway/src/main/resources/static/css/launch.css`. Pozwoli to łatwo rozróżnić te dwa wdrożenia. Uruchom następujące polecenie, aby skompilować pakiet jar: 

    ```azurecli
    mvn clean package
    ```

1. Utwórz nowe wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, nadając mu nazwę wdrożenia przejściowego "zielony".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Po pomyślnym zakończeniu wdrożenia uzyskaj dostęp do strony bramy z poziomu **pulpitu nawigacyjnego aplikacji** i Zobacz wszystkie wystąpienia na karcie **wystąpienia aplikacji** po lewej stronie.
  
> [!NOTE]
> Stan odnajdywania to "OUT_OF_SERVICE", dzięki czemu ruch nie zostanie rozesłany do tego wdrożenia przed ukończeniem weryfikacji.

## <a name="verify-the-staging-deployment"></a>Weryfikowanie wdrożenia przemieszczania

1. Wróć do strony **zarządzania wdrożeniem** i wybierz nowe wdrożenie. Stan wdrożenia powinien zawierać wartość **uruchomiony**. Przycisk "Przypisz/Cofnij przypisanie domeny" zostanie wyłączony, ponieważ jest środowiskiem przejściowym.

1. Na stronie **Przegląd** powinien zostać wyświetlony **punkt końcowy testu**. Skopiuj i wklej go do nowej strony przeglądarki. powinna zostać wyświetlona nowa strona metryki Piggy.

>[!TIP]
> * Upewnij się, że punkt końcowy testu jest zakończony znakiem "/", aby upewnić się, że arkusz CSS został poprawnie załadowany.  
> * Jeśli przeglądarka wymaga wprowadzenia poświadczeń logowania w celu wyświetlenia strony, użyj [dekodowania adresu URL](https://www.urldecoder.org/) , aby zdekodować punkt końcowy testu. Dekodowanie adresu URL zwraca adres URL w postaci "https://\<username >: \<Password > @ \<cluster-Name >. test. azureapps. IO/Gateway/Green".  Użyj tej metody, aby uzyskać dostęp do punktu końcowego.

>[!NOTE]    
> Ustawienia serwera konfiguracji mają zastosowanie do środowiska przejściowego, a także do produkcji. Na przykład jeśli ustawisz ścieżkę kontekstu (`server.servlet.context-path`) dla bramy aplikacji na serwerze konfiguracji jako *somepath*, ścieżka do zielonej zmiany wdrożenia: "https://\<username >: \<password > @ \<cluster-Name >. test. azureapps. IO/ Brama/Zielona/somepath/... "
 
 Jeśli w tym momencie odwiedzasz swoją publiczną bramę aplikacji, powinna zostać wyświetlona stara strona bez nowej zmiany.
    
## <a name="set-the-green-as-production-deployment"></a>Ustawianie zielonego jako wdrożenia produkcyjnego

1. Zweryfikowano zmianę w środowisku przejściowym, można wypchnąć ją do środowiska produkcyjnego. Wróć do **zarządzania wdrożeniem** i zaznacz pole wyboru przed aplikacją "Gateway".
2. Wybierz pozycję "Ustaw wdrożenie".
3. Wybierz pozycję "zielony" z menu "wdrożenie produkcyjne" i wybierz pozycję **Zastosuj** .
4. Przejdź do strony **przeglądu** aplikacji bramy. Jeśli masz już przypisaną domenę dla aplikacji bramy, adres URL zostanie wyświetlony na stronie **Przegląd** . Przejdź do adresu URL i zobaczysz zmodyfikowaną stronę metryk Piggy.

>[!NOTE]
> Po ustawieniu zielonego wdrożenia na środowisko produkcyjne poprzednie wdrożenie przejdzie do wdrożenia przejściowego.

## <a name="modify-the-staging-deployment"></a>Modyfikowanie wdrożenia przemieszczania

Jeśli zmiany nie są zadowalające, możesz zmodyfikować kod aplikacji, utworzyć nowy pakiet jar i przekazać go do zielonego wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Usuwanie wdrożenia przemieszczania

Usuń wdrożenie przemieszczania z portu platformy Azure, przechodząc do strony wdrożenia przemieszczania i wybierając przycisk **Usuń** .

Alternatywnie możesz usunąć wdrożenie przejściowe z interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
