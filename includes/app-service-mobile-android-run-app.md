---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240315"
---
1. Otwórz projekt za pomocą środowiska **Android Studio** przy użyciu opcji **Importuj projekt (Eclipse ADT, Gradle itp.)**. Pamiętaj, aby zaznaczyć tę opcję importu, aby uniknąć błędów JDK.

2. Otwórz plik `ToDoActivity.java` w tym folderze - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Nazwa aplikacji `ZUMOAPPNAME`to .

3. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. Na `Overview` bloku poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład - nazwa witryny dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

4. W `onCreate()` metodzie `ZUMOAPPURL` zastąp parametr publicznym punktem końcowym powyżej.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    Staje się
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Naciśnij przycisk **Uruchom „aplikację”**, aby skompilować projekt i uruchomić aplikację w symulatorze systemu Android.

4. W aplikacji wpisz sensowny tekst, na przykład *Ukończ samouczek,* a następnie kliknij przycisk "Dodaj". To spowoduje wysłanie żądania POST do wdrożonej wcześniej wewnętrznej bazy danych Azure. Wewnętrzna baza danych wstawia dane z żądania do tabeli SQL TodoItem i zwraca informacje na temat przechowywanych od niedawna elementów do aplikacji mobilnej. W aplikacji mobilnej dane te są wyświetlane na liście.
    ![Szybki start systemu Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)