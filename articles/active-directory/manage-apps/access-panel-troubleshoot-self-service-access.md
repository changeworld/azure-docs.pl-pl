---
title: Problem z dostępem do samoobsługi | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów związanych z dostępem do aplikacji samoobsługowych
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784281"
---
# <a name="problem-using-self-service-application-access"></a>Problem z dostępem do samoobsługi aplikacji

Dostęp do samoobsługi aplikacji to świetny sposób, aby umożliwić użytkownikom samodzielne wykrywanie aplikacji, opcjonalnie zezwolić grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. Można zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników dla aplikacji logowania jednokrotnego hasła bezpośrednio z ich paneli dostępu.

Zanim użytkownicy będą mogli samodzielnie wykrywać aplikacje z panelu dostępu, należy włączyć **samoobsługowy dostęp do aplikacji** do wszystkich aplikacji, do których chcesz zezwolić użytkownikom na samodzielne wykrywanie i żądanie dostępu.

## <a name="general-issues-to-check-first"></a>Kwestie ogólne do sprawdzenia w pierwszej kolejności

-   Upewnij się, że dostęp do aplikacji samoobsługowej jest poprawnie skonfigurowany. Zobacz "Jak skonfigurować dostęp do samoobsługi aplikacji".

-   Upewnij się, że użytkownik lub grupa została włączona, aby zażądać dostępu do aplikacji samoobsługowej.

-   Upewnij się, że użytkownik odwiedza odpowiednie miejsce dostępu do aplikacji samoobsługowej. użytkownicy mogą przejść do [swojego Panelu dostępu do aplikacji](https://myapps.microsoft.com/) i kliknąć przycisk **+Dodaj,** aby znaleźć aplikacje, do których włączono dostęp samoobsługowy.

-   Jeśli dostęp do samoobsługi aplikacji został niedawno skonfigurowany, po kilku minutach spróbuj zalogować się i ponownie zalogować do panelu dostępu użytkownika, aby sprawdzić, czy pojawiły się zmiany w dostępie samoobsługowym.

## <a name="how-to-configure-self-service-application-access"></a>Jak skonfigurować dostęp do samoobsługi aplikacji

Aby włączyć samoobsługowy dostęp aplikacji do aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz włączyć dostęp samoobsługowy z listy.

7. Po załadowaniu aplikacji kliknij **samoobsługę** z menu nawigacji po lewej stronie aplikacji.

8. Aby włączyć dostęp do aplikacji samoobsługi dla tej aplikacji, należy włączyć **Yes.** **zezwalaj użytkownikom na żądanie dostępu do tej aplikacji?**

9. Następnie, aby wybrać grupę, do której użytkownicy, którzy żądają dostępu do tej aplikacji, należy kliknąć selektor obok etykiety **Do której grupy należy dodać przypisanych użytkowników?** i wybierz grupę.

10. **Opcjonalnie:** Jeśli chcesz wymagać zatwierdzenia biznesowego, zanim użytkownicy będą mogli uzyskać dostęp, ustaw opcję **Yes** **Wymagaj zatwierdzenia przed udzieleniem dostępu do tej aplikacji?**

11. **Opcjonalnie: W przypadku aplikacji używających hasła logowania jednokrotnego tylko,** jeśli chcesz zezwolić osobom zatwierdzające firmy na określenie haseł wysyłanych do tej aplikacji dla **Yes**zatwierdzonych użytkowników, ustaw opcję Zezwalaj osobom zatwierdzające na **ustawianie haseł użytkownika dla tej aplikacji?**

12. **Opcjonalnie:** Aby określić osoby zatwierdzające firmy, które mogą zatwierdzać dostęp do tej aplikacji, kliknij selektor obok etykiety **Kto może zatwierdzić dostęp do tej aplikacji?**

    >[!NOTE]
    > Grupy nie są obsługiwane.
    >
    >

13. **Opcjonalnie:** **W przypadku aplikacji, które udostępniają role**, jeśli chcesz przypisać do roli zatwierdzonych użytkowników samoobsługowych, kliknij selektor obok roli Do której **użytkownicy powinni być przypisani w tej aplikacji?**

14. Kliknij przycisk **Zapisz** w górnej części bloku, aby zakończyć.

Po zakończeniu konfiguracji aplikacji samoobsługowej użytkownicy mogą przejść do [panelu dostępu](https://myapps.microsoft.com/) do aplikacji i kliknąć przycisk **+Dodaj,** aby znaleźć aplikacje, do których włączono dostęp samoobsługowy. Osoby zatwierdzające firmy widzą również powiadomienie w [panelu dostępu do aplikacji](https://myapps.microsoft.com/). Można włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażąda dostępu do aplikacji, która wymaga ich zatwierdzenia. 

Te zatwierdzenia obsługują tylko pojedyncze przepływy pracy zatwierdzania, co oznacza, że jeśli określisz wiele osób zatwierdzających, każda pojedyncza osoba zatwierdzająca może zatwierdzić dostęp do aplikacji.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu 

otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/ramy czasowe podczas wystąpienia błędu

-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../users-groups-roles/groups-self-service-management.md)
