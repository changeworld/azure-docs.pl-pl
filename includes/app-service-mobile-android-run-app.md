---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240315"
---
1. Otwórz projekt za pomocą środowiska **Android Studio** przy użyciu opcji **Importuj projekt (Eclipse ADT, Gradle itp.)** . Pamiętaj, aby zaznaczyć tę opcję importu, aby uniknąć błędów JDK.

2. Otwórz plik `ToDoActivity.java` w tym folderze - ZUMOAPPNAME/app/src/main/java/com/przykład/zumoappname. Nazwa aplikacji jest `ZUMOAPPNAME`.

3. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do aplikacji mobilnej, który został utworzony. Na `Overview` bloku, zwróć uwagę na adres URL, który jest publiczny punkt końcowy dla twojej aplikacji mobilnej. Przykład — nazwa witryny, dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

4. W `onCreate()` metody, Zastąp `ZUMOAPPURL` parametru z publicznym punktem końcowym powyżej.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    staje się
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Naciśnij przycisk **Uruchom „aplikację”** , aby skompilować projekt i uruchomić aplikację w symulatorze systemu Android.

4. W aplikacji wpisz łatwy do rozpoznania tekst, np. *Ukończ samouczek*, a następnie kliknij przycisk Dodaj. To spowoduje wysłanie żądania POST do wdrożonej wcześniej wewnętrznej bazy danych Azure. Wewnętrzna baza danych wstawia dane z żądania do tabeli SQL TodoItem i zwraca informacje na temat przechowywanych od niedawna elementów do aplikacji mobilnej. W aplikacji mobilnej dane te są wyświetlane na liście.
    ![Przewodnik Szybki Start systemu Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)