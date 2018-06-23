---
title: Jak używać dostęp do aplikacji Sklep internetowy | Dokumentacja firmy Microsoft
description: Włącz dostęp do aplikacji Sklep internetowy umożliwia użytkownikom znajdowanie własne aplikacje
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: japere,asteen
ms.openlocfilehash: da44fb2982c76b849181683f308e414c3d3f0b7a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331210"
---
# <a name="how-to-use-self-service-application-access"></a>Jak używać dostęp do aplikacji Sklep internetowy

Zanim użytkownicy mogą odnajdować własnym aplikacji z ich panel dostępu, należy włączyć **dostęp do aplikacji Sklep internetowy** do dowolnych aplikacji, które chcesz zezwolić użytkownikom na własnym odnajdywania i żądania dostępu do.

Ta funkcja jest to doskonały sposób, można oszczędzić czas i pieniądze jako grupa IT i zdecydowanie zaleca się jako część wdrożenia nowoczesnych aplikacji w usłudze Azure Active Directory.

Za pomocą tej funkcji, można:

-   Zezwala użytkownikom na własnym odnajdywanie aplikacji z [panelu dostępu aplikacji](https://myapps.microsoft.com/) bez bothering grupę IT.

-   Dodaj tych użytkowników do wstępnie skonfigurowanej grupy, aby zobaczyć, kto żąda dostępu, usunięcie dostępu i zarządzanie rolami do nich przypisane.

-   Opcjonalnie zezwolić osoba zatwierdzająca biznesowych do zatwierdzenia żądania dostępu do aplikacji, nie ma grupa IT.

-   Opcjonalnie można skonfigurować maksymalnie 10 użytkowników indywidualnych, którzy mogą zatwierdzić dostęp do tej aplikacji.

-   Opcjonalnie zezwolić firma osoba zatwierdzająca Ustaw hasła użytkowników można użyć do logowania do aplikacji, z biznesowe osoby zatwierdzającej [panelu dostępu aplikacji](https://myapps.microsoft.com/).

-   Opcjonalnie automatycznie przypisywać samoobsługi bezpośrednio przypisać użytkowników do roli aplikacji.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włącz dostęp do aplikacji Sklep internetowy umożliwia użytkownikom znajdowanie własne aplikacje

Dostęp do aplikacji Sklep internetowy jest to dobry sposób, aby zezwolić użytkownikom na własnym odnajdywanie aplikacji, opcjonalnie zezwolić grupie biznesowej, aby zatwierdzić dostęp do tych aplikacji. Możesz zezwolić grupie biznesowej do zarządzania poświadczeniami przypisane do tych użytkowników do prawej jednokrotnego hasła w aplikacji z ich paneli dostępu.

Aby włączyć samoobsługowe aplikacji dostęp do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **Azure Portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby umożliwić samoobsługi dostęp do z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **samoobsługi** z menu nawigacji po lewej stronie aplikacji.

8.  Aby włączyć dostęp do aplikacji Sklep internetowy dla tej aplikacji, należy włączyć **Zezwalaj użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz, aby **tak.**

9.  Następnie wybierz grupę, do których użytkownicy, którzy żądają dostępu do tej aplikacji można dodać, kliknij przycisk wyboru obok etykiety **do grupy, do której ma zostać dodany przypisanych użytkowników?** i wybierz grupę.

10. **Opcjonalnie:** aby wymagają zatwierdzenia biznesowych, przed użytkownicy mają dostęp, ustaw **wymagają zatwierdzenia przed udzieleniem im dostępu do tej aplikacji?** Przełącz, aby **tak**.

11. **Opcjonalnie: dla aplikacji za pomocą hasła jednokrotnego na tylko** Jeśli chcesz umożliwić tych osób zatwierdzających firm określić hasła, które są wysyłane do tej aplikacji dla zatwierdzonych użytkowników, ustawić **Zezwalaj osób zatwierdzających do ustawienia użytkownika hasła dla tej aplikacji?**  Przełącz, aby **tak**.

12. **Opcjonalnie:** do określenia osób zatwierdzających biznesowych, którzy mogą zatwierdzić dostęp do tej aplikacji, kliknij przycisk wyboru obok etykiety **kto może zatwierdzić dostęp do tej aplikacji?** do wybranych do 10 osób zatwierdzających biznesowych.

   * Grupy nie są obsługiwane.

13. **Opcjonalnie:** **dla aplikacji, które ujawnia ról**, jeśli chcesz przypisać do roli użytkowników samoobsługi zatwierdzone, kliknij selektor **do roli należy użytkowników można przypisać w tej aplikacji?** Wybierz rolę, do której można przypisać tych użytkowników.

14. Kliknij przycisk **zapisać** na górze bloku, aby zakończyć.

Po zakończeniu konfiguracji samoobsługi aplikacji, użytkownicy mogą przechodzić do ich [panelu dostępu aplikacji](https://myapps.microsoft.com/) i kliknij przycisk **+ Dodaj** przycisk, aby znaleźć aplikacji, dla których włączono samoobsługi dostęp. Osób zatwierdzających firm również wyświetlone powiadomienie w ich [panelu dostępu aplikacji](https://myapps.microsoft.com/). Można włączyć wiadomość e-mail z informacją, gdy użytkownik żąda dostępu do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługuje pojedynczy przepływów pracy, co oznacza, że jeśli określisz wiele osób zatwierdzających żadnych jedna osoba zatwierdzająca może zatwierdzić dostęp do aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie usługi Azure Active Directory do zarządzania grupami samoobsługi](active-directory-accessmanagement-self-service-group-management.md)
