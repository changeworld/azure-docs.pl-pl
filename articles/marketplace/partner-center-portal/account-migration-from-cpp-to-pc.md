---
title: Migracja konta z portal Cloud Partner do Centrum partnerskiego — komercyjna witryna Marketplace na platformie Azure
description: Jak migrować konto z przeglądarki CPP do Centrum partnerskiego. — Komercyjne witryny Marketplace na platformie Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 13cbd799a1ffb877ace2231bfb854764edac9c90
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147125"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migracja konta z portal Cloud Partner do Centrum partnerskiego

Jeśli masz istniejące konto CPP, Twoje ustawienia konta muszą zostać zmigrowane do Centrum partnerskiego.

## <a name="account-migration-process"></a>Proces migracji konta

Jeśli jesteś użytkownikiem z rolą właściciela w programie CPP dla danego konta, na stronie Twojego profilu wydawcy zostanie wyświetlony żółty baner. Może należeć do jednego z następujących dwóch przypadków:

- Twoje konto zostało już zmigrowane i możesz przystąpić do zarządzania ustawieniami konta w centrum partnerskim.
- Aby ukończyć migrację konta do Centrum partnerskiego, należy wykonać kilka dalszych czynności.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Twoje konto zostało zmigrowane do Centrum partnerskiego

W przypadku wszystkich kont, które ukończyły migrację z programu CPP do Centrum partnerskiego, zarządzanie kontami odbywa się w centrum partnerskim. Zmiany, takie jak dodawanie/usuwanie użytkowników, zostaną zsynchronizowane z powrotem do programu CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Twoje konto nie zostało jeszcze zmigrowane do Centrum partnerskiego

Kliknij transparent, aby zainicjować proces migracji konta. Oczekiwane jest wprowadzenie następujących elementów:

1. Służbowy adres e-mail: <br> <br> W większości przypadków Zaloguj się przy użyciu adresu e-mail używanego do logowania się do programu CPP. W niektórych przypadkach należy użyć innego adresu e-mail:

    * Konto Microsoft: Jeśli konto CPP jest konto Microsoft, musisz wprowadzić prawidłowy służbowy adres e-mail skojarzony z dzierżawcą, dla którego zarejestrowano identyfikator MPN.

    * Niezgodność dzierżawy: Jeśli służbowy adres e-mail nie należy do dzierżawy skojarzonej z IDENTYFIKATORem Microsoft Partner Network na Twoim koncie CPP, zostanie wyświetlony komunikat o błędzie. Aby przenieść poprzedni błąd, wprowadź adres e-mail skojarzony z dzierżawcą. Komunikat o błędzie zawiera nazwę dzierżawcy.

2. Utwórz konto w programie Microsoft Partner Network

    W przypadku, gdy konto CPP nie ma identyfikatora Microsoft Partner Network lub ma jeden nieprawidłowy, musisz zarejestrować się w programie Microsoft Partner Network w ramach procesu aktywacji.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Utwórz konto w programie Microsoft Partner Network

Firmy, które chcą mieć partnerów firmy Microsoft, muszą przyłączyć się do Microsoft Partner Network (MPN) i uzyskać identyfikator MPN. Jeśli jesteś już członkiem Microsoft Partner Network i masz identyfikator MPN, upewnij się, że jest on dostępny w ramach procesu aktywacji konta.  

Jeśli nie jesteś jeszcze członkiem Microsoft Partner Network, możesz [dołączyć teraz](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) , aby uzyskać identyfikator MPN. Pamiętaj, aby zanotować swój identyfikator MPN, ponieważ musisz go wprowadzić podczas procesu aktywacji konta.

Aby dowiedzieć się więcej na temat Microsoft Partner Network, zobacz [Dołączanie Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) w witrynie sieci Web partnera. Aby dowiedzieć się więcej na temat dołączania do Microsoft Partner Network oferowanych przez niezależnych dostawców oprogramowania, zobacz [Centrum zasobów niezależnego dostawcy oprogramowania](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Przenoszenie rozwiązań opartych na protokole Dynamics 365 do Centrum partnerskiego

Jeśli utworzono program Dynamics 365 dla zaangażowania klienta lub Dynamics 365 dla rozwiązań Finansów i operacji w jednym portalu komercyjnym partnera GTM, **rozwiązania te powinny być teraz zarządzane w centrum partnerskim**.

**Jeśli nie przeniesiono swoich rozwiązań do 31 sierpnia 2019**, wykonaj poniższe kroki najszybciej, jak to możliwe. Dopóki tego nie zrobisz:

- Niezależni dostawcy oprogramowania nie będą mieć dostępu do korzyści marketingowych
- Nastąpi utrata priorytetu dla współsprzedawcy
- Te, które wymagają osadzenia w chmurze, będą niezgodne po 15 października 2019

> [!NOTE]
> Jeśli Twoje konto członkostwa MPN zostało pierwotnie utworzone w centrum członkostwa partnera (PMC), zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , aby upewnić się, że Twoje konto zostało zmigrowane przed wykonaniem poniższych kroków. Jeśli zobaczysz ekran profilu z IDENTYFIKATORem MPN, wszystko jest gotowe do wykonania. W przeciwnym razie należy uruchomić migrację konta, postępując zgodnie z instrukcjami w [Centrum członkostwa w partnerze](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Jeśli potrzebujesz pomocy, odwiedź [Pomoc techniczną](https://partner.microsoft.com/support?issueid=100-0077).

1. Przejdź do [strony Przegląd komercyjnego portalu Marketplace w centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Jeśli w okienku nawigacji po lewej stronie zostanie wyświetlona wartość "komercyjna witryna Marketplace", nastąpi rejestracja i przechodzenie do następnego kroku. Jeśli nie, [zarejestruj się teraz w portalu komercyjnym](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .

2. Potwierdź, że oferty są w AppSource, [wyszukując oferty](https://appsource.microsoft.com/). Jeśli oferty znajdują się już w AppSource, przejdź do następnego kroku. W przypadku dowolnej oferty nieznajdującej się w AppSource Utwórz [nową ofertę dynamics 365 dla programu Customer Engagement](create-new-customer-engagement-offer.md) lub [nową ofertę usługi Dynamics 365 dla operacji](create-new-operations-offer.md).

3. Sprawdź swoją rejestrację w programie Business Applications ISV Connect:
   * Upewnij się, że na stronie [umowy](https://partner.microsoft.com/dashboard/account/agreements) w centrum partnerskim zostało zaakceptowane **uzupełnienie Business Applications niezależnego dostawcy oprogramowania** , aby zarejestrować się w programie.
   * Na stronie [Ustawienia konta](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) podaj informacje o rozliczeniach.

4. Prześlij wszystkie nowe i istniejące oferty do certyfikacji, nawet jeśli Twoje oferty zostały wcześniej certyfikowane. Jeśli to możliwe, możesz poprosić o uczestnictwo w warstwie Premium w trakcie tego procesu. Jeśli Twoja oferta została wcześniej certyfikowana, **należy przeprowadzić ponowną certyfikację aplikacji do 15 października 2019.** Certyfikacja lub Recertyfikacja wymaga, aby aplikacja obsługiwała najnowszą wersję naszej platformy Business Applications.

5. Przejdź do [portalu komercyjnego GTM](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) i Dodaj adres URL listy AppSource w sekcji linki witryny Marketplace. Jeśli potrzebujesz pomocy w tym kroku, Wyślij wiadomość e-mail na cosell@microsoft.comadres.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim](./manage-account.md) 
