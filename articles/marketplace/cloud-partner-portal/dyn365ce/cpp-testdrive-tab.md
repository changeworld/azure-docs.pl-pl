---
title: Dynamics 365 Customer Engagement aplikacji oferują kartę wersji testowej | Portal Azure Marketplace
description: Jak skonfigurować wersję testową dla Dynamics 365 Customer Engagement aplikacji oferty w witrynie Marketplace usługi AppSource.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5bb5f39ef5f5bce09a8639ba9eedc6d042e60c1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942349"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 Customer Engagement aplikacji testowej karta

Użyj **wersji testowej** kartę, aby utworzyć środowisko próbne dla swoich klientów.  Daje klientom z wersją próbną praktyczne, przeprowadzanemu samodzielnie procesowi kluczowe funkcje i korzyści, przedstawione w scenariuszu wdrożenia rzeczywistych swojej oferty.  Z dostępnych opcji wersji próbnej wersji testowej jest najbardziej efektywne, wysokiej jakości generowania potencjalnych klientów i zwiększonej konwersji tych potencjalnych klientów.  Aby uzyskać więcej informacji, zobacz [co to jest wersja testowa?](../test-drive/what-is-test-drive.md)

Środowisko wersję testową aplikacji Dynamics 365, która jest automatycznie uruchamia jako rozwiązanie hostowanych przez firmę Microsoft.  Aby uzyskać więcej informacji, zobacz [wersji testowej hostowana](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive).

Na karcie wersja testowa będzie mieć trzy sekcje potencjalnych: **Wersja testowa**, **szczegóły**, i **konfiguracji technicznej**.  Ostatnie dwie sekcje są wyświetlane tylko po włączeniu funkcji wersji testowej.  Znak gwiazdki (*) dodanym na końcu nazwy pola wskazuje, że jest to wymagane. 


## <a name="test-drive-section"></a>Sekcja dysku testu

Aby włączyć tę funkcję, wybierz pozycję **tak** do **włączyć wersję testową**.


## <a name="details-section"></a>Sekcja szczegóły

Podaje się podstawowe informacje o wersji testowej w **szczegóły** sekcji.   

![Sekcji szczegółowe informacje o wersji testowej](./media/test-drive-tab-details.png)

W poniższej tabeli opisano pola wymagane do skonfigurowania wersję testową aplikacji Dynamics 365. Wymagane pola są wskazanych przez znak gwiazdki (*).

|      Pole                    |    Opis                  |
|    ---------                  |  ---------------                |
|      Opis elementu\*            |   Opisz, co można zrobić w wersji testowej. Aby sformatować ten opis, można użyć podstawowa tagów HTML. Na przykład &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;i nagłówki.  |
|  Podręcznik użytkownika\*                |   Przekaż podręcznika użytkownika używanego przez klientów do zaprezentowania środowisko wersji testowej. Ten dokument musi być plikiem PDF. |
|  Test Drive demonstracyjny materiał wideo (opcjonalnie) |  Możesz podać przewodnik wideo dotyczący wersji testowej. Klient może Obejrzyj ten film, zanim one wypróbować wersję testową. Podaj adres URL pliku wideo w usłudze YouTube lub Vimeo. Jeśli wybierzesz **+ Dodaj wideo**, zostanie wyświetlony monit o podanie następujących informacji:<ul><li>Name (Nazwa)</li><li>Adres URL</li><li>Miniatura (w formacie PNG, 533 x 324 pikseli)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Sekcja konfiguracji Technical Preview

w tej sekcji należy podać szczegółowe informacje techniczne o wersji testowej.

![Sekcji szczegółowe informacje o wersji testowej](./media/test-drive-tab-tech-config.png)

Gdy pola mają następujące cele.  Wymagane pola są wskazanych przez znak gwiazdki (*).

|      Pole                    |    Opis                  |
|    ---------                  |  ---------------                |
| Typ wersji testowej\*            | Wybierz **(Dynamics 365 for Customer Engagement) hostowanych przez Microsoft**.  |
| Wersje testowe maksymalnej liczby równoczesnych\*    | Liczba równoczesnych wystąpień active wersji testowej w dowolnym czasie. Każdy użytkownik zużyje licencją Dynamics podczas ich wersji testowej jest aktywny, więc należy upewnić się, że masz co najmniej następującą liczbę licencji Dynamics dostępne dla użytkowników wersji testowej. Zalecanej wartości wynoszącej 3 – 5.  |
| Dysk czas trwania testu (godziny)\*   | Maksymalna liczba godzin użytkownika wersji testowej wystąpienia będą aktywne dla. Po tym czasie zostanie przekroczony, wystąpienie będzie można anulować aprowizację w dzierżawie. Zalecana wartość 2 – 24 godzin w zależności od złożoności aplikacji. Użytkownik może zażądać innej wersji testowej zawsze, jeśli zabrakło czasu, a ma zostać ponownie oceniona.  |
| Adres URL wystąpienia\*                  | Adres URL wersji testowej początkowo spowoduje przejście do. Zazwyczaj jest to adres URL wystąpienia usługi Dynamics 365, która aplikacja ma i przykładowe dane są zainstalowane na urządzeniu.  |
| Identyfikator dzierżawy usługi Azure AD\*            | Identyfikator GUID dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Aby pobrać tę wartość, zaloguj się do witryny Azure portal i przejdź do **usługi Azure Active Directory** > **wybierz właściwości** > **Skopiuj identyfikator katalogu**.  |
| Identyfikator aplikacji w usłudze Azure AD\*               | Identyfikator GUID aplikacji usługi Azure AD  |
| Klucz aplikacji w usłudze Azure AD\*              | Klucz tajny aplikacji usługi Azure AD, na przykład: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Nazwa dzierżawy usługi Azure AD\*          | Nazwa dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Użyj formatu < tenantname. >. onmicrosoft.com, na przykład: `testdrive.onmicrosoft.com`  |
| Adres URL wystąpienia interfejsu API sieci Web\*          | Interfejs API sieci Web adres URL wystąpienia usługi Dynamics 365. Tę wartość można pobrać, logując się do swojego wystąpienia programu Microsoft Dynamics 365 i przechodząc do **ustawienia** > **dostosowywania** > **dla deweloperów Zasoby** > **wystąpienia interfejsu Web API (kopia tego adresu URL)** . Przykładowa wartość: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Nazwa roli\*                     | Nazwa niestandardowej roli zabezpieczeń Dynamics 365 dla wersji testowej zostały utworzone i przypisane do użytkowników po uruchomieniu, na przykład `testdriveuser`. |
|  |  |

Po podaniu wymaganych informacji wybierz **Zapisz**.


## <a name="next-steps"></a>Kolejne kroki

Następnie będzie podać informacje o sprzedaży i marketingu w [karty Szczegóły Storefront](./cpp-storefront-details-tab.md).

