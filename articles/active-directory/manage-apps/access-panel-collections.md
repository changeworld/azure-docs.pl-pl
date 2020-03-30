---
title: Tworzenie kolekcji dla portali Moje aplikacje w usłudze Azure Active Directory | Dokumenty firmy Microsoft
description: Użyj kolekcji Moje aplikacje, aby dostosować strony Moje aplikacje, aby ułatwić użytkownikom końcowym korzystanie z aplikacji Moje aplikacje. Organizowanie aplikacji w grupy z osobnymi kartami.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120087"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Tworzenie kolekcji w portalu Moje aplikacje

Użytkownicy mogą używać portalu Moje aplikacje do wyświetlania i uruchamiania aplikacji opartych na chmurze, do których mają dostęp. Domyślnie wszystkie aplikacje, do których użytkownik może uzyskać dostęp, są wyświetlane razem na jednej stronie. Aby lepiej zorganizować tę stronę dla użytkowników, jeśli masz licencję Usługi Azure AD Premium P1 lub P2, możesz skonfigurować kolekcje. Za pomocą kolekcji można grupować aplikacje, które są powiązane (na przykład według roli zadania, zadania lub projektu) i wyświetlać je na osobnej karcie. Kolekcja zasadniczo stosuje filtr do aplikacji, do których użytkownik może już uzyskać dostęp, więc użytkownik widzi tylko te aplikacje w kolekcji, które zostały przypisane do nich.

> [!NOTE]
> W tym artykule opisano, jak administrator może włączać i tworzyć kolekcje. Aby uzyskać informacje dla użytkownika końcowego dotyczące korzystania z portalu i kolekcji Moje aplikacje, zobacz [Uzyskiwanie dostępu do kolekcji i korzystanie z nich](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-the-latest-my-apps-features"></a>Włączanie najnowszych funkcji Usługi Moje aplikacje

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako administrator użytkownika lub administrator globalny.

2. Przejdź do**ustawień użytkownika**usługi Azure **Active Directory** > .

3. W obszarze **Podgląd funkcji użytkownika**wybierz pozycję **Zarządzaj ustawieniami podglądu funkcji użytkownika**.

4. W obszarze **Użytkownicy mogą korzystać z funkcji podglądu w moich aplikacjach**wybierz jedną z następujących opcji:
   * **Wybrano** — włącza operacje dla określonej grupy. Użyj opcji **Wybierz grupę,** aby wybrać grupę, dla której chcesz włączyć funkcje.  
   * **Wszystko** — umożliwia korzystanie z funkcji dla wszystkich użytkowników.

> [!NOTE]
> Aby otworzyć portal Moje aplikacje, `https://myapps.microsoft.com` użytkownicy mogą użyć łącza lub `https://myapps.microsoft.com/contoso.com`dostosowanego łącza dla twojej organizacji, takiego jak . Po włączeniu nowego środowiska Moje aplikacje **zaktualizowany program Moje aplikacje będzie dostępny** w górnej części strony Moje aplikacje, a użytkownicy mogą wybrać **pozycję Wypróbuj,** aby wyświetlić nowe środowisko. Aby przestać korzystać z nowego środowiska, użytkownicy mogą wybrać **opcję Tak** z banera **Pozostaw nowe środowisko** u góry strony.

## <a name="create-a-collection"></a>Tworzenie kolekcji

Aby utworzyć kolekcję, musisz mieć licencję Usługi Azure AD Premium P1 lub P2.

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako administrator za pomocą licencji Usługi Azure AD Premium P1 lub P2.

2. Przejdź do **usługi Azure Active Directory** > **Enterprise Applications**.

3. W obszarze **Zarządzaj**wybierz pozycję **Kolekcje**.

4. Wybierz **pozycję Nowa kolekcja**. Na **stronie Nowa kolekcja** wprowadź **nazwę** kolekcji (nie zaleca się używania "kolekcji" w nazwie. Następnie wprowadź **opis**.

   ![Nowa strona kolekcji](media/acces-panel-collections/new-collection.png)

5. Wybierz kartę **Aplikacje.** Wybierz **+ Dodaj aplikację**, a następnie na stronie Dodawanie **aplikacji** zaznacz wszystkie aplikacje, które chcesz dodać do kolekcji, lub użyj pola **Wyszukiwania,** aby znaleźć aplikacje.

   ![Dodawanie aplikacji do kolekcji](media/acces-panel-collections/add-applications.png)

6. Po zakończeniu dodawania aplikacji wybierz pozycję **Dodaj**. Zostanie wyświetlona lista wybranych aplikacji. Za pomocą strzałek w górę można zmienić kolejność aplikacji na liście. Aby przenieść aplikację w dół lub usunąć ją z kolekcji, wybierz menu **Więcej** (**...**).

7. Wybierz kartę **Właściciele.** Wybierz **+ Dodaj użytkowników i grupy**, a następnie na stronie Dodawanie użytkowników i **grup** wybierz użytkowników lub grupy, do których chcesz przypisać własność. Po zakończeniu wybierania użytkowników i grup wybierz pozycję **Wybierz**.

9. Wybierz kartę **Użytkownicy i grupy.** Wybierz **+ Dodaj użytkowników i grupy**, a następnie na stronie Dodawanie użytkowników i **grup** wybierz użytkowników lub grupy, do których chcesz przypisać kolekcję. Możesz też użyć pola **wyszukiwania,** aby znaleźć użytkowników lub grupy. Po zakończeniu wybierania użytkowników i grup wybierz pozycję **Wybierz**.

   ![Dodawanie użytkowników i grup](media/acces-panel-collections/add-users-and-groups.png)

11. Wybierz **pozycję Recenzja + Utwórz**. Właściwości nowej kolekcji są wyświetlane.


## <a name="view-audit-logs"></a>Wyświetlanie dzienników inspekcji

Dzienniki inspekcji rejestrują operacje kolekcji my apps, w tym akcje tworzenia kolekcji użytkownika końcowego. Następujące zdarzenia są generowane z moich aplikacji:

* Tworzenie kolekcji
* Edytuj kolekcję
* Usuwanie kolekcji
* Uruchamianie aplikacji (użytkownik końcowy)
* Samoobsługowe dodawanie aplikacji (użytkownik końcowy)
* Samoobsługowe usuwanie aplikacji (użytkownik końcowy)

Dzienniki inspekcji można uzyskać w [portalu Azure,](https://portal.azure.com) wybierając**dzienniki inspekcji** aplikacji usługi Azure **Active Directory** > **Enterprise w** > sekcji Działanie. W **obszarze Usługa**wybierz pozycję Moje **aplikacje**.

## <a name="get-support-for-my-account-pages"></a>Uzyskaj pomoc techniczną dla stron Moje konto

Na stronie Moje aplikacje użytkownik może wybrać pozycję **Moje konto** > **Wyświetl moje konto,** aby otworzyć ustawienia konta. Na stronie **Moje konto** usługi Azure AD użytkownicy mogą zarządzać informacjami zabezpieczającymi, urządzeniami, hasłami i nie tylko. Mogą również uzyskać dostęp do ustawień konta pakietu Office.

Jeśli musisz przesłać żądanie pomocy technicznej dotyczące problemu ze stroną konta usługi Azure AD lub na stronie konta pakietu Office, wykonaj następujące kroki, aby żądanie było prawidłowo kierowane: 

* W przypadku problemów ze stroną **"Moje konto" usługi Azure AD** otwórz żądanie pomocy technicznej z poziomu witryny Azure portal. Przejdź do **witryny Azure portal** > **Azure Active Directory** > **Nowe żądanie pomocy technicznej**.

* W przypadku problemów ze stroną **"Moje konto" pakietu Office** otwórz żądanie pomocy technicznej z poziomu centrum administracyjnego usługi Microsoft 365. Przejdź do >  **centrum administracyjnego usługi Microsoft 365****.** 

## <a name="next-steps"></a>Następne kroki
[Środowisko użytkownika końcowego dla aplikacji w usłudze Azure Active Directory](end-user-experiences.md)