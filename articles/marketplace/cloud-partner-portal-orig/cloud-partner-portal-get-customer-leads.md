---
title: Konfigurowanie potencjalnych klientów | Portal Azure Marketplace
description: W portalu Cloud Partner, należy skonfigurować potencjalnych klientów.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: c3e483a33d986eff767b3529e30208319ad90b23
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935001"
---
<a name="get-customer-leads"></a>Pozyskiwanie potencjalnych klientów
==================

W tym artykule opisano sposób tworzenia potencjalnych klientów za pomocą portalu Cloud Partner. Można połączyć z takich potencjalnych klientów z systemem CRM i Zintegruj swój potok sprzedaży.

## <a name="leads"></a>Potencjalni klienci

Potencjalni klienci czy klienci, którzy są zainteresowani lub wdrażasz swoje produkty z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) lub [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Klient przyjmuje "Wersji testowej" oferty. Wersje testowe są jej jako przyspieszonej możliwości, której możesz wyrazić swoją firmę natychmiast do potencjalnych klientów bez żadnych bariery. Wersje testowe Generowanie potencjalnych klientów dla klienta, który jest zainteresowany podjęcie próby produktu, aby dowiedzieć się więcej. Dowiedz się więcej na temat wersji testowych w [Azure Marketplace testowej](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Przykłady dysku testu witryny Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Klient wyraża zgodę na udostępnianie swoich informacji po wybraniu pozycji "Pobierz ją teraz". Jest to potencjalny klient **początkową zainteresowań** potencjalnych klientów, którym udostępniamy informacje o kliencie, który wyraził zainteresowania podczas pobierania produktu. Potencjalny klient jest wyrównanie do góry nabycia lejka.

   ![Pobierz teraz opcję](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Klient wybierze "Kup" w [witryny Azure Portal](https://portal.azure.com/) można pobrać produktu. Jest to potencjalny klient **active** potencjalnych klientów, w którym możemy udostępniać informacje o kliencie, który został uruchomiony, aby wdrożyć Twój produkt.

   ![Kup opcję](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Klienta trwało "Wersji testowej" oferty. Wersje testowe są jej jako przyspieszonej możliwości, której możesz wyrazić swoją firmę natychmiast do potencjalnych klientów bez żadnych bariery. Wersje testowe Generowanie potencjalnych klientów w klienta, który jest zainteresowana wypróbować produkt w taki sposób, aby dowiedzieć się więcej. Dowiedz się więcej na temat wersji testowych w [wersji próbnej usługi Appsource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Przykład dysku testu](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Klient wyraża zgodę na udostępnianie swoich informacji po wybraniu pozycji "Pobierz ją teraz". Jest to potencjalny klient **początkową zainteresowań** potencjalnego klienta, w którym udostępniamy informacje o kliencie, który wyraża zainteresowanie wprowadzenie produktu. Potencjalny klient jest wyrównanie do góry nabycia lejka.

      ![Pobierz teraz opcję](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Klient wybierze "Kontakt ze mną" w swojej oferty. Jest to potencjalny klient **active** potencjalnych klientów, którym udostępniamy informacje o kliencie, który pyta, do której z dotyczących produktu.

    ![Kontakt ze mną w — opcja](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Data realizacji
---------

Poszczególnymi potencjalnymi klientami, otrzymany podczas procesu pozyskiwania klientów ma dane w określonych polach. Ponieważ otrzymasz potencjalni klienci z wielu kroków, najlepszym sposobem obsługi potencjalnych klientów jest cofnąć zduplikowane i Personalizuj przegapisz odpowiedzi członka. Dzięki temu każdy klient otrzymuje odpowiedni komunikat i tworzenia relacji.

### <a name="lead-source"></a>Źródło potencjalnych klientów

Format Źródło potencjalnych klientów jest **źródła**-**akcji** |  **oferty**

**Źródła**: "AzureMarketplace", "Witryna Azure Portal", "TestDrive" i "Usługa AppSource (SPZA)"

**Akcje**:
- "Dodatki"--instalacji. Ta akcja znajduje się w portalu Azure Marketplace lub usługi AppSource, po zakupie produktu.
- "Czas ładowania strony"--stoisk partnera ds. wersji próbnej. Ta akcja jest w usłudze AppSource, gdy klient używa kontakt ze mną opcji.
- "DNC" — nie należy kontaktować się. Ta akcja jest w usłudze AppSource, gdy pobiera zażądał można skontaktować się z partnerem, który został między wymienione na stronie aplikacji. Udostępniamy głowic, że ten klient był krzyżowe w górę na liście w aplikacji, ale nie musisz otrzymywać informacji.
- "Utwórz" — Ta akcja jest tylko w witrynie Azure Portal i jest generowany, gdy klient zakupi oferty do swojego konta.
- "StartTestDrive" — Ta akcja dotyczy tylko w przypadku testowania dyski i jest generowany, gdy klient uruchamia ich wersji testowej.

**Oferty**

Poniższe przykłady pokazują unikatowe identyfikatory, które są przypisane do wydawcy i oferty określone: checkpoint.check punktu r77-10sg-byol, bitnami.openedxcypress i docusign.3701c77e-1cfa - 4c 56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informacje o kliencie

W polach w poniższym przykładzie wyświetlane informacje o kliencie, który jest zawarty w potencjalnego klienta.
- Imię: Jan
- Nazwisko: Smith
- Adres e-mail: jsmith\@microsoft.com
- Telefon: 1234567890
- Kraj: USA
- Firmy: Microsoft
- Tytuł: DYREKTOR DS. TECHNOLOGII

>[!Note]
>Nie wszystkie dane w poprzednim przykładzie jest zawsze dostępna dla każdego potencjalnego klienta.

Aktywnie pracujemy nad udoskonalanie potencjalnych klientów, więc jeśli wystąpią pole danych, które nie są widoczne w tym miejscu, ale chcesz mieć [przesłać nam swoją opinię](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak połączyć CRM system z portalu Cloud Partner
------------------------------------------------------------

Aby rozpocząć pobieranie potencjalnych klientów, utworzyliśmy naszych łącznika prowadzić zarządzania w portalu Cloud Partner, aby łatwo podłączyć w informacjach o CRM, a firma Microsoft będzie wprowadzać połączenie dla Ciebie. Teraz można łatwo korzystać z potencjalnymi klientami, wygenerowany przez Portal marketplace bez znaczących nakładów pracy inżynierów do integracji z systemem zewnętrznym.

![Łącznik zarządzania potencjalnych klientów](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Firma Microsoft można napisać tabeli potencjalnych klientów w różnych systemów CRM lub bezpośrednio do usługi Azure Storage umożliwiający zarządzanie potencjalnymi klientami jednak chcesz. Każdy z poniższych linków zawierają instrukcje dotyczące łączenia realizacji możliwych miejsc docelowych:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) Aby uzyskać instrukcje dotyczące sposobu konfigurowania usługi Dynamics CRM Online w celu uzyskania potencjalnych klientów.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) Aby uzyskać instrukcje dotyczące konfigurowania usługi Marketo prowadzić konfiguracji można pobrać potencjalnych klientów.
-    [SalesForce](./cloud-partner-portal-lead-management-instructions-salesforce.md) Aby uzyskać instrukcje dotyczące konfigurowania wystąpienia usługi Salesforce, można pobrać potencjalnych klientów.
-    [Usługa Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md) Aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure storage w celu uzyskania potencjalnych klientów w tabeli platformy Azure.
-   [Punkt końcowy HTTPS](./cloud-partner-portal-lead-management-instructions-https.md) Aby uzyskać instrukcje dotyczące konfigurowania punktu końcowego Https można pobrać potencjalnych klientów.

Po skonfigurowaniu lokalizacji docelowej potencjalnych klientów i opublikować ofertę, utworzymy sprawdzić poprawność połączenia i wysyłać realizacji testu. Podczas przeglądania oferty przed przejściem na żywo możesz przetestować połączenie z potencjalnymi klientami, próbując uzyskać samodzielnie ofertę w środowisku w wersji zapoznawczej. Należy się upewnić, że swoje aktualne pobytu ustawienia potencjalnych klientów tak, aby nie utracić potencjalnych klientów, więc upewnij się, że aktualizowanie tych połączeń, zawsze wtedy, gdy coś, co zmieniło się na swojej stronie.

<a name="what-next"></a>Co dalej?
----------

Po technicznych, skonfiguruj znajduje się w miejscu, należy dołączyć potencjalni klienci w bieżącej sprzedaży i strategii marketingowej i procesów operacyjnych. Jesteśmy bardzo zainteresowani lepiej zrozumieć całkowitej sprzedaży, przetwarzania i aby ściśle współpracować z Tobą na dostarczaniu wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, które zapewnią Ci sukces. Chętnie poznamy Twoją opinię, jak firma Microsoft optymalizacji i ulepszanie potencjalnych klientów, który możemy wysłać dodatkowe dane, aby sprawić, że ci klienci pomyślnie. Daj nam znać, jeśli interesują Cię [opiniowania](mailto:AzureMarketOnboard@microsoft.com) i sugestie, aby ułatwić zespołowi sprzedaży korzystać z portalu Marketplace potencjalnych klientów.
