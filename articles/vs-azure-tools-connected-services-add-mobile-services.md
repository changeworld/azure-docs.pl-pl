---
title: Dodawanie usługi Mobile Services przy użyciu usług połączonych w programie Visual Studio | Dokumentacja firmy Microsoft
description: Dodawanie usługi Mobile Services przy użyciu okna dialogowego programu Visual Studio Dodaj połączone usługi
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: ''
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: d185fdafebad56f8970e390b2a0672c3fb84df8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884216"
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Dodawanie usługi Mobile Services przy użyciu programu Visual Studio usług połączonych
Z programem Visual Studio 2015, możesz nawiązać połączenie przy użyciu usług Azure Mobile Services **dodać podłączonej usługi** okna dialogowego. Możesz połączyć się z wszystkich aplikacji klienckich C#, dowolnej aplikacji JavaScript lub aplikacji Cordova i platform. Po nawiązaniu połączenia można utworzyć i uzyskać dostęp do danych, tworzenie niestandardowych interfejsów API i zaplanowanych zadań lub dodać obsługę powiadomień wypychanych.  Operacja podłączonych usług dodaje wszystkie odpowiednie odwołania i kod połączenia. Mogą również czerpać korzyści z wbudowaną obsługę uwierzytelniania przy użyciu różnych popularnych tożsamości systemów, takich jak Azure AD, Facebook, Twitter i Accounts firmy Microsoft.

## <a name="supported-project-types"></a>Typy obsługiwane projektów
> [!NOTE]
> W programie Visual Studio 2015 Dodawanie usług Azure Mobile Services dla projektów uniwersalnych systemu Windows (Windows 10) za pomocą okna dialogowego Dodawanie połączenia usług nie jest obsługiwane. Możesz dodać usług Azure Mobile Services przez zainstalowanie odpowiednich pakietów za pomocą Menedżera pakietów NuGet dla projektu.
> 
> 

Okno dialogowe usług połączonych służy do nawiązania połączenia usługi Azure Mobile Services w poniższych typów projektów.

* Projekty Sklepu Windows 8.1 .NET, Phone i aplikacji uniwersalnych
* Projekty Sklepu Windows 8.1 JavaScript, Phone i aplikacji uniwersalnych
* Projektów utworzonych za pomocą narzędzi Visual Studio Tools for Apache Cordova

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Połączyć się za pomocą okna dialogowego Dodawanie usług połączonych usług Azure Mobile Services
1. Upewnij się, że masz konta platformy Azure. Jeśli nie masz konta platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Otwórz **dodać usług połączonych** okno dialogowe.
   
   * Dla aplikacji .NET Otwórz projekt w programie Visual Studio, otwórz menu kontekstowe dla **odwołania** węzła w Eksploratorze rozwiązań, a następnie wybierz pozycję **dodać podłączonej usługi**
     
        ![Nawiązywanie połączenia z usługą Azure Mobile Services](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * W przypadku projektów aplikacji oprogramowania Apache Cordova, otwórz projekt w programie Visual Studio, otwórz menu kontekstowe dla węzła projektu w Eksploratorze rozwiązań, a następnie wybierz **dodać podłączonej usługi**.
3. W **dodać podłączonej usługi** okna dialogowego wybierz **usług Azure Mobile Services**, a następnie wybierz pozycję **Konfiguruj** przycisku. Może pojawić się prośba do zalogowania się do platformy Azure, jeśli jeszcze tego nie zrobiono.
   
    ![Dodawanie usługi mobilnej Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. W **usług Azure Mobile Services** oknie dialogowym Wybierz istniejącą usługę mobilną, jeśli istnieje. Jeśli chcesz utworzyć nową usługę mobilną Azure, wykonaj procedurę przedstawioną poniżej, aby to zrobić. W przeciwnym razie przejdź do następnego kroku.
   
    Aby utworzyć nowe konto usługi mobilnej:
   
   1. Wybierz ** Utwórz usługę ** łącze u dołu okna dialogowego.
       ![Dodawanie nowej podłączonej usługi mobilnej](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. Na **Tworzenie usługi mobilnej** okno dialogowe, można wybrać usługę mobilną zaplecze JavaScript lub usługę mobilną zaplecza .NET z **środowiska uruchomieniowego** listy rozwijanej. 
      
       ![Tworzenie usługi mobilnej](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       Usługi zaplecza JavaScript jest prosta i skuteczna. Po utworzeniu usługi mobilnej zaplecze JavaScript kod JavaScript po stronie serwera są przechowywane w chmurze, ale skrypty serwera można edytować za pomocą Eksploratora serwera lub portalu zarządzania Azure. 
      
       Usługi mobilnej zaplecza .NET umożliwia pełną moc i elastyczność interfejsu API sieci Web oraz Entity Framework. Po utworzeniu usługi mobilnej zaplecza .NET projekt jest tworzony i dodany do rozwiązania. 
   3. Wybierz **Region** której mają usługę mobilną, a następnie wprowadź nazwę użytkownika i hasło dla serwera.
   4. Po wprowadzeniu wymaganych informacji, wybierz **Utwórz** przycisk, aby utworzyć usługę mobilną.
   5. Nową usługę mobilną powinny pojawiać się na liście usług na **usług Azure Mobile Services** okno dialogowe. Wybierz nową usługę mobilną na liście, a następnie wybierz **Dodaj** przycisk, aby dodać usługę do projektu.
5. Przejrzyj stronę wprowadzenie pobierania zostanie wyświetlony i dowiedzieć się, jak projekt został zmodyfikowany. Po dodaniu podłączonej usługi, w przeglądarce zostanie wyświetlona strona wprowadzenie. Przejrzyj sugerowane kolejne kroki i przykłady kodu lub przełącz się do strony co się stało, aby zobaczyć, jakie odwołania zostały dodane do projektu, i jak zostały zmodyfikowane pliki kodu i konfiguracji.
6. Za pomocą przykłady kodu przedstawiono wskazówki Uruchom, pisanie kodu w celu uzyskania dostępu do usługi mobilnej!

## <a name="how-your-project-is-modified"></a>Jak zmienić jest projektu
Jak Visual Studio modyfikuje projekt zależy od typu projektu. C# aplikacji klienta, zobacz [co się stało — projektów C#](http://go.microsoft.com/fwlink/p/?LinkId=513119). W przypadku aplikacji klienckich JavaScript, zobacz [co się stało — projekty JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). Dla aplikacji Cordova zobacz [co się stało — projektów Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Następne kroki
Zadawaj pytania i uzyskiwanie pomocy: 

* [MSDN Forum: Usług Azure Mobile Services](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Azure Mobile Services w blogu zespołu usługi Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)
* [Azure Mobile Services w witrynie azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)
* [Dokumentacja usług Azure Mobile Services w witrynie azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

