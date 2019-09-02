---
title: Migracja konta z portal Cloud Partner do Centrum partnerskiego — komercyjna witryna Marketplace na platformie Azure
description: Jak migrować konto z przeglądarki CPP do Centrum partnerskiego. — Komercyjne witryny Marketplace na platformie Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 661adb755d076b0cc6114a287855482165ccb817
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208094"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migracja konta z portal Cloud Partner do Centrum partnerskiego

Gdy oferty zostaną zmigrowane z programu portal Cloud Partner (CPP) do Centrum partnerskiego (komputer), zostaną one zablokowane do edycji w programie CPP. W tym momencie ustawienia konta muszą zostać zmigrowane do Centrum partnerskiego. Zarówno ustawienia konta, jak i oferty, można zarządzać w centrum partnerskim.

## <a name="account-migration-process"></a>Proces migracji konta

Gdy oferty są migrowane z programu CPP, Twoje konto jest skonfigurowane do migracji. 
 
Jeśli jesteś użytkownikiem z rolą właściciela w programie CPP dla danego konta, na stronie Twojego profilu wydawcy zostanie wyświetlony żółty baner. Zostanie wyświetlony monit o przeniesienie ustawień konta do Centrum partnerskiego. 

Kliknij transparent, aby zainicjować proces migracji konta. Oczekiwane jest wprowadzenie następujących elementów:

### <a name="work-email-address"></a>Służbowy adres e-mail

W większości przypadków Zaloguj się przy użyciu adresu e-mail używanego do logowania się do programu CPP. W niektórych przypadkach należy użyć innego adresu e-mail:

* Konto Microsoft: Jeśli konto CPP jest konto Microsoft, musisz wprowadzić prawidłowy służbowy adres e-mail skojarzony z dzierżawcą, dla którego zarejestrowano identyfikator MPN.

* Niezgodność dzierżawy: Jeśli służbowy adres e-mail nie należy do dzierżawy skojarzonej z IDENTYFIKATORem Microsoft Partner Network na Twoim koncie CPP, zostanie wyświetlony komunikat o błędzie. Aby przenieść poprzedni błąd, wprowadź adres e-mail skojarzony z dzierżawcą. Komunikat o błędzie zawiera nazwę dzierżawcy.

### <a name="sign-up-for-microsoft-partner-network-program"></a>Utwórz konto w programie Microsoft Partner Network

W przypadku, gdy konto CPP nie ma identyfikatora Microsoft Partner Network lub ma jeden nieprawidłowy, musisz zarejestrować się w programie Microsoft Partner Network w ramach procesu aktywacji.

## <a name="after-account-migration-is-complete"></a>Po zakończeniu migracji konta

Migracja musi wystąpić tylko raz dla danego konta. Po zakończeniu migracji konta przez danego partnera wszyscy właściciele będą widzieć to zachowanie na stronie profilu wydawcy:

1. Zostanie wyświetlona strona Ustawienia partnera w Microsoft Partner Network, w której można teraz zarządzać ustawieniami konta. 
2. Żółty transparent na stronie profilu wydawcy w programie CPP będzie widoczny dla użytkowników, którzy mają rolę właściciela, z prośbą o zarządzanie ustawieniami konta w centrum partnerskim. 
3. Strona Ustawienia konta w programie CPP jest konwertowana na tryb tylko do odczytu. 

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Przenoszenie rozwiązań opartych na protokole Dynamics 365 do Centrum partnerskiego

Jeśli utworzono program Dynamics 365 dla zaangażowania klienta lub Dynamics 365 dla rozwiązań Finansów i operacji w jednym portalu komercyjnym partnera GTM, **rozwiązania te powinny być teraz zarządzane w centrum partnerskim**.

**Jeśli nie przeniesiono swoich rozwiązań do 31 sierpnia 2019**, wykonaj poniższe kroki najszybciej, jak to możliwe. Dopóki tego nie zrobisz: 
- Niezależni dostawcy oprogramowania nie będą mieć dostępu do korzyści marketingowych
- Nastąpi utrata priorytetu dla współsprzedawcy
- Te, które wymagają osadzenia w chmurze, nie będą zgodne

> [!NOTE]
> Jeśli Twoje konto członkostwa MPN zostało pierwotnie utworzone w centrum członkostwa partnera (PMC), zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , aby upewnić się, że Twoje konto zostało zmigrowane przed wykonaniem poniższych kroków. Jeśli zobaczysz ekran profilu z IDENTYFIKATORem MPN, wszystko jest gotowe do wykonania. W przeciwnym razie należy uruchomić migrację konta, postępując zgodnie z instrukcjami w [Centrum członkostwa](https://partners.microsoft.com/partnerprogram/Welcome.aspx)w partnerze. Jeśli potrzebujesz pomocy, odwiedź [Pomoc techniczną](https://partner.microsoft.com/support?issueid=100-0077).

1. Przejdź do [strony Przegląd komercyjnego portalu Marketplace w centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Jeśli w okienku nawigacji po lewej stronie zostanie wyświetlona wartość "komercyjna witryna Marketplace", nastąpi rejestracja i przechodzenie do następnego kroku. Jeśli nie, [zarejestruj się teraz w portalu komercyjnym](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
2. Potwierdź, że oferty są w AppSource, [wyszukując oferty](https://appsource.microsoft.com/). Jeśli oferty znajdują się już w AppSource, przejdź do następnego kroku. W przypadku dowolnej oferty nieznajdującej się w AppSource Utwórz [nową ofertę dynamics 365 dla programu Customer Engagement](create-new-customer-engagement-offer.md) lub [nową ofertę usługi Dynamics 365 dla operacji](create-new-operations-offer.md).
3. Sprawdź swoją rejestrację w programie Business Applications ISV Connect:
  
   * Upewnij się, że na stronie [umowy](https://partner.microsoft.com/dashboard/account/agreements) w centrum partnerskim zostało zaakceptowane **uzupełnienie Business Applications niezależnego dostawcy oprogramowania** , aby zarejestrować się w programie.
   * Na stronie [Ustawienia konta](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) podaj informacje o rozliczeniach.

4. Prześlij wszystkie nowe i istniejące oferty do certyfikacji, nawet jeśli Twoje oferty zostały wcześniej certyfikowane. Jeśli to możliwe, możesz poprosić o uczestnictwo w warstwie Premium w trakcie tego procesu. Jeśli Twoja oferta została wcześniej certyfikowana, **należy przeprowadzić ponowną certyfikację aplikacji do 15 października 2019.** Certyfikacja lub Recertyfikacja wymaga, aby aplikacja obsługiwała najnowszą wersję naszej platformy Business Applications.
5. Przejdź do [portalu komercyjnego GTM](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) i Dodaj adres URL listy AppSource w sekcji linki witryny Marketplace. Jeśli potrzebujesz pomocy w tym kroku, Wyślij wiadomość e-mail na cosell@microsoft.comadres.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim](./manage-account.md) 
