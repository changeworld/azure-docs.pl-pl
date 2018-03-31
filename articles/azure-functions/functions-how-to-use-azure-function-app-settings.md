---
title: Konfiguruj ustawienia aplikacji funkcji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia aplikacji funkcji platformy Azure.
services: ''
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 8974b2ca243da5dd2f7202c66602f7254496d040
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Jak zarządzać aplikacji funkcji w portalu Azure 

W środowisku Azure Functions aplikacji funkcji udostępnia kontekst wykonania dla poszczególnych funkcji. Funkcja zachowania aplikacji mają zastosowanie do wszystkich funkcji obsługiwanych przez aplikację danej funkcji. W tym temacie opisano sposób konfigurowania i zarządzania aplikacjami funkcji w portalu Azure.

Aby rozpocząć, przejdź do [portalu Azure](http://portal.azure.com) i zaloguj się do konta platformy Azure. Na pasku wyszukiwania w górnej części portalu wpisz nazwę aplikacji funkcji i wybierz ją z listy. Po wybraniu aplikacji funkcji, można znaleźć na następującej stronie:

![Funkcja Przegląd aplikacji w portalu Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Funkcje ulubionych w portalu 

Czasami może być trudne do znalezienia zasobów w [portalu Azure]. Aby ułatwić znajdowanie aplikacje funkcji, które zostały utworzone, należy dodać aplikacje funkcji do ulubionych w portalu. 

1. Zaloguj się do witryny [portalu Azure].

2. Kliknij strzałkę w lewym dolnym rogu, aby rozwinąć wszystkie usługi, wpisz `Functions` w polu **Filtr**, a następnie kliknij przycisk gwiazdki obok pozycji **Aplikacje funkcji**.  
 
    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    Powoduje to dodanie ikony Funkcje do menu po lewej stronie portalu.

3. Zamknij menu, a następnie przewiń w dół, aby wyświetlić ikonę Funkcje. Kliknij tę ikonę, aby wyświetlić listę wszystkich aplikacji funkcji. Kliknij aplikację funkcji, aby pracować z funkcjami w tej aplikacji. 
 
    ![Funkcja aplikacji w ulubionych](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[portalu Azure]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Karta Ustawienia aplikacji — funkcja

![Funkcja Przegląd aplikacji w portalu Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

**Ustawienia** karta jest, której można zaktualizować wersji środowiska uruchomieniowego funkcje używane przez aplikację funkcji. Możliwe jest również, w których zarządzasz klucze hosta używany do ograniczania dostępu HTTP do wszystkich funkcji obsługiwanych przez aplikację funkcji.

Funkcje obsługuje hosting zużycia i hosting planów usługi aplikacji. Aby uzyskać więcej informacji, zobacz [wybierz plan prawidłowe usługi dla usługi Azure Functions](functions-scale.md). Dla lepszej przewidywalności w planie zużycia funkcje umożliwia ograniczenie użycia platformy ustawiając dziennego limitu przydziału wykorzystania w gigabajtach sekund. Po osiągnięciu dziennego limitu przydziału wykorzystania funkcji aplikacji zostanie zatrzymana. Aplikacja funkcji zatrzymana z powodu osiągnięcia limitu wydatków przydziału można ponownie włączyć w tym samym kontekście ustalaniu codziennie wydatków przydziału. Zobacz [usługi Azure Functions cennikiem](http://azure.microsoft.com/pricing/details/functions/) szczegółowe informacje dotyczące rozliczeń.   

## <a name="platform-features-tab"></a>Karta funkcje platformy

![Funkcja kartę funkcje platformy aplikacji.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Funkcja aplikacje uruchomione w i są obsługiwane przez platformę Azure App Service. W efekcie aplikacji funkcji mają dostęp do większość funkcji hosta platformy web core platformy Azure. **Funkcji platformy** jest karta umożliwiający dostęp do wielu funkcji używanej w aplikacjach funkcji platformy aplikacji usługi. 

> [!NOTE]
> Nie wszystkie funkcje usługi aplikacji są dostępne, po uruchomieniu aplikacji funkcji na zużycie plan hostingu.

Pozostała część tego tematu koncentruje się na następujące funkcje usługi App Service w portalu Azure, które są przydatne w przypadku funkcji:

+ [Edytor usługi aplikacji](#editor)
+ [Ustawienia aplikacji](#settings) 
+ [Console](#console)
+ [Zaawansowane narzędzia (Kudu)](#kudu)
+ [Opcje wdrażania](#deployment)
+ [CORS](#cors)
+ [Uwierzytelnianie](#auth)
+ [Definicja interfejsu API](#swagger)

Aby uzyskać więcej informacji na temat pracy z ustawieniami usługi aplikacji, zobacz [Konfigurowanie ustawień usługi aplikacji Azure](../app-service/web-sites-configure.md).

### <a name="editor"></a>Edytor usługi aplikacji

| | |
|-|-|
| ![Funkcja aplikacji edytora usługi aplikacji.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Edytor usługi aplikacji to zaawansowany edytor w portalu można modyfikować pliki konfiguracyjne w formacie JSON i podobne plików kodu. Wybranie tej opcji spowoduje uruchomienie na karcie przeglądarki oddzielne za pomocą edytora podstawowego. Dzięki temu można zintegrować z repozytorium Git, uruchom debugowanie kodu i modyfikowanie ustawień funkcji aplikacji. Ten edytor udostępnia środowisko ulepszone dla funkcji w porównaniu z bloku domyślnym funkcji aplikacji.    |

![Edytor usługi aplikacji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Ustawienia aplikacji

| | |
|-|-|
| ![Funkcja aplikacji ustawienia aplikacji.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | Usługi aplikacji **ustawienia aplikacji** bloku jest gdzie Konfigurowanie i zarządzanie nimi framework w wersji, debugowania zdalnego, ustawienia aplikacji i parametry połączenia. Po zintegrowaniu aplikacji funkcji z innymi usługami innych firm i Azure można modyfikować tych ustawień w tym miejscu. Aby usunąć ustawienie, przewiń w prawo i wybierz **X** ikonę na prawym końcu wiersza (tego nie pokazano na poniższej ilustracji).

![Konfigurowanie ustawień aplikacji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Konsoli

| | |
|-|-|
| ![Funkcja konsoli aplikacji w portalu Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | Konsoli w portalu jest narzędziem idealne developer, zamiast do interakcji z aplikacją funkcji w wierszu polecenia. Typowe polecenia obejmują katalogu i tworzenie plików i nawigacji, a także wykonywanie plików wsadowych i skryptów. |

![Funkcja aplikacji konsoli](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Zaawansowane narzędzia (Kudu)

| | |
|-|-|
| ![Funkcja aplikacji Kudu w portalu Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Zaawansowane narzędzia dla aplikacji usługi (znanej także jako Kudu) zapewniają dostęp do zaawansowanych funkcji administracyjnych funkcji aplikacji. Program Kudu do zarządzania informacje o systemie, ustawienia aplikacji, zmienne środowiskowe, rozszerzenia lokacji, nagłówków HTTP i zmiennych serwera. Można również uruchomić **Kudu** przechodząc do punktu końcowego SCM dla funkcji aplikacji, takie jak `https://<myfunctionapp>.scm.azurewebsites.net/` |

![Skonfiguruj Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opcje wdrażania

| | |
|-|-|
| ![Funkcja opcje wdrażania aplikacji w portalu Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funkcje pozwala tworzyć kodu funkcji na komputerze lokalnym. Następnie możesz przekazać projektu aplikacji funkcji lokalnej na platformie Azure. Oprócz tradycyjnego przekazywania FTP funkcje umożliwia wdrażanie aplikacji przy użyciu rozwiązań popularnych ciągłej integracji, takich jak GitHub, programu VSTS Dropbox, Bitbucket i innych funkcji. Aby uzyskać więcej informacji, zobacz [ciągłego wdrażania usługi Azure Functions](functions-continuous-deployment.md). Aby przekazać ręcznie przy użyciu protokołu FTP lub lokalnego Git, również należy [skonfigurować poświadczenia wdrażania](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Funkcja aplikacji CORS w portalu Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Aby zapobiec wykonaniu złośliwego kodu w usługach, usługa aplikacji blokuje wywołania funkcji aplikacji ze źródeł zewnętrznych. Funkcje obsługuje współużytkowanie zasobów między źródłami (CORS), umożliwiają definiowanie "dozwolone" dozwolonych źródeł, z których funkcji może akceptować żądań zdalnych do udostępniania.  |

![Konfigurowanie funkcji aplikacji CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Uwierzytelnianie

| | |
|-|-|
| ![Funkcja uwierzytelniania aplikacji w portalu Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Korzystając z funkcji wyzwalacz protokołu HTTP, można wymagać wywołania najpierw zostać uwierzytelnione. Usługi aplikacji obsługuje uwierzytelnianie usługi Azure Active Directory i zaloguj się przy użyciu dostawców sieci społecznościowych, takich jak Facebook, firma Microsoft i Twitter. Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania określonych dostawców, zobacz [omówienie uwierzytelniania w usłudze Azure App Service](../app-service/app-service-authentication-overview.md). |

![Konfigurowanie uwierzytelniania dla aplikacji funkcja](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definicja interfejsu API

| | |
|-|-|
| ![Funkcja aplikacji z interfejsu API programu swagger definicji w portalu Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Funkcje obsługuje struktury Swagger, aby umożliwić klientom łatwo korzystać z funkcji wyzwalanych przez protokół HTTP. Aby uzyskać więcej informacji na temat tworzenia definicji interfejsu API z programu Swagger, odwiedź stronę [hosta interfejsu API RESTful z CORS w usłudze Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Umożliwia także funkcje serwera proxy do definiowania pojedynczą powierzchnię interfejsu API dla wielu funkcji. Aby uzyskać więcej informacji, zobacz [Praca z serwerów proxy funkcji Azure](functions-proxies.md). |

![Konfigurowanie funkcji aplikacji interfejsu API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Kolejne kroki

+ [Konfigurowanie ustawień usługi aplikacji Azure](../app-service/web-sites-configure.md)
+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)



