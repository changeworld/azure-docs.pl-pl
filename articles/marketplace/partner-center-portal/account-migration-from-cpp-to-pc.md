---
title: Migracja konta z portal Cloud Partner do Centrum partnerskiego — komercyjna witryna Marketplace na platformie Azure
description: Jak migrować konto z przeglądarki CPP do Centrum partnerskiego. — Komercyjne witryny Marketplace na platformie Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.openlocfilehash: 9ab9a57641e6b34942ae6d4293311714177aa012
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533191"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migracja konta z portal Cloud Partner do Centrum partnerskiego

Po przeprowadzeniu migracji ofert z programu z portal Cloud Partner (CPP) do Centrum partnerskiego (komputer) można je zablokować do edycji w programie CPP. W tym momencie ustawienia konta muszą zostać zmigrowane do Centrum partnerskiego.  Zarówno ustawienia konta, jak i oferty, można zarządzać w centrum partnerskim.

## <a name="account-migration-process"></a>Proces migracji konta

Po przeprowadzeniu migracji ofert z programu do CPP Twoje konto jest skonfigurowane do migracji. 
 
Jeśli jesteś użytkownikiem z rolą właściciela w programie CPP dla danego konta, na stronie Twojego profilu wydawcy zostanie wyświetlony żółty baner.  Zostanie wyświetlony monit o przeniesienie ustawień konta do Centrum partnerskiego. 

Kliknij transparent, aby zainicjować proces migracji konta. Oczekiwane jest wprowadzenie następujących elementów:

### <a name="work-e-mail-address"></a>**Służbowy adres e-mail**

W większości przypadków Zaloguj się przy użyciu adresu e-mail używanego do logowania się do programu CPP. W niektórych przypadkach należy użyć innego adresu e-mail:

* Konto Microsoft: Jeśli konto CPP jest kontem Microsoft, należy wprowadzić prawidłowy służbowy adres e-mail skojarzony z dzierżawcą, dla którego zarejestrowano identyfikator MPN. 

* Niezgodność dzierżawy: Jeśli służbowy adres e-mail nie należy do dzierżawy skojarzonej z IDENTYFIKATORem Microsoft Partner Network na Twoim koncie CPP, zostanie wyświetlony komunikat o błędzie. Aby przenieść poprzedni błąd, wprowadź adres e-mail skojarzony z dzierżawcą. Komunikat o błędzie zawiera nazwę dzierżawcy. 

### <a name="sign-up-for-microsoft-partner-network-program"></a>Utwórz konto w programie Microsoft Partner Network

W przypadku, gdy konto CPP nie ma identyfikatora Microsoft Partner Network lub ma jeden nieprawidłowy, musisz zarejestrować się w programie Microsoft Partner Network w ramach procesu aktywacji.

## <a name="account-activation-is-complete"></a>Aktywacja konta została zakończona

Migracja konta musi nastąpić tylko raz dla danego konta. Po zakończeniu migracji konta przez danego partnera wszyscy właściciele będą widzieć to zachowanie na stronie profilu wydawcy:

1. Zostanie wyświetlona strona Ustawienia partnera w Microsoft Partner Network, w której można zarządzać ustawieniami konta partnera firmy Microsoft. 
2. Po zakończeniu migracji konta na stronie profilu wydawcy widoczny jest żółty baner, który jest widoczny dla użytkowników należących do roli właściciel w programie CPP dla danego konta, z prośbą o zarządzanie ustawieniami konta w centrum partnerskim. 
3. Strona Ustawienia konta w programie CPP jest następnie konwertowana do trybu tylko do odczytu. 

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Przenoszenie rozwiązań opartych na protokole Dynamics 365 do Centrum partnerskiego

Jeśli masz Dynamics 365 dla zaangażowania klienta lub Dynamics 365 dla rozwiązań Finansów i operacji w jednym portalu komercyjnym GTM, postępuj **zgodnie z tymi instrukcjami do 31 sierpnia 2019** , aby przenieść te rozwiązania do Centrum partnerskiego.

> [!NOTE]
> Jeśli Twoje konto zostało pierwotnie utworzone w centrum członkostwa partnera (PMC), zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , aby upewnić się, że Twoje konto zostało zmigrowane przed wykonaniem poniższych kroków. Jeśli zobaczysz ekran profilu z IDENTYFIKATORem MPN, wszystko jest gotowe do wykonania. W przeciwnym razie należy uruchomić migrację konta, postępując zgodnie z instrukcjami w [Centrum członkostwa](https://partners.microsoft.com/partnerprogram/Welcome.aspx)w partnerze. Jeśli potrzebujesz pomocy, odwiedź [Pomoc techniczną](https://partner.microsoft.com/support?issueid=100-0077).

1. Przejdź do [strony Przegląd komercyjnego portalu Marketplace w centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Jeśli w okienku nawigacji po lewej stronie zostanie wyświetlona wartość "komercyjna witryna Marketplace", nastąpi rejestracja i przechodzenie do następnego kroku. Jeśli nie, [zarejestruj się teraz w portalu komercyjnym](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
2. Potwierdź, że oferty są w AppSource, [wyszukując oferty](https://appsource.microsoft.com/). Jeśli oferty znajdują się już w AppSource, przejdź do następnego kroku. W przypadku dowolnej oferty nieznajdującej się w AppSource Utwórz [nową ofertę dynamics 365 dla programu Customer Engagement](create-new-customer-engagement-offer.md) lub [nową ofertę usługi Dynamics 365 dla operacji](create-new-operations-offer.md).
3. Sprawdź swoją rejestrację w programie Business Applications ISV Connect:
  
   * Upewnij się, że na stronie [umowy](https://partner.microsoft.com/dashboard/account/agreements) w centrum partnerskim zostało zaakceptowane **uzupełnienie Business Applications niezależnego dostawcy oprogramowania** , aby zarejestrować się w programie.
   * Na stronie [Ustawienia konta](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) podaj informacje o rozliczeniach.

4. Prześlij wszystkie nowe i istniejące oferty do certyfikacji, nawet jeśli Twoje oferty zostały wcześniej certyfikowane. **Zalecamy przesłanie najszybciej, jak to możliwe, aby zapewnić wystarczającą ilość czasu na zatwierdzenie przed 31 sierpnia 2019.**
5. Przejdź do [portalu komercyjnego GTM](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) i Dodaj adres URL listy AppSource w sekcji linki witryny Marketplace. Jeśli potrzebujesz pomocy w tym kroku, Wyślij wiadomość e-mail na cosell@microsoft.comadres.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim](./manage-account.md) 
