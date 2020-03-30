---
title: Konfigurowanie potencjalnych klientów | Azure Marketplace
description: Konfigurowanie potencjalnych klientów w portalu cloud partnerów.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280324"
---
<a name="get-customer-leads"></a>Pozyskiwanie potencjalnych klientów
==================

W tym artykule wyjaśniono, jak utworzyć potencjalnych klientów za pomocą portalu cloud partner. Można podłączyć te przewody do systemu CRM, i zintegrować je do rurociągu sprzedaży.

## <a name="leads"></a>Leads

Potencjalni klienci to klienci zainteresowani lub wdrażający twoje produkty z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) lub [AppSource.](https://appsource.microsoft.com)

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Klient przyjmuje "Test Drive" twojej oferty. Jazdy testowe to przyspieszona okazja do natychmiastowego dzielenia się swoją firmą z potencjalnymi klientami bez żadnych barier wejścia na rynek. Wszystkie dyski testowe generują potencjalnego klienta, który jest zainteresowany wypróbowaniem produktu, aby dowiedzieć się więcej. Dowiedz się więcej o dyskach testowych na [dysku testowym w portalu Azure Marketplace](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Przykłady dysków testowych w portalu Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Klient wyraża zgodę na udostępnianie swoich informacji po wybraniu opcji "Pobierz teraz". Ten potencjalny klient jest początkowym potencjalnym **klientem,** w którym dzielimy się informacjami o kliencie, który wyraził zainteresowanie uzyskaniem produktu. Ołów jest górną częścią lejka akwizycyjnego.

   ![Pobierz teraz opcję](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Klient wybiera "Zakup" w [witrynie Azure Portal,](https://portal.azure.com/) aby uzyskać produkt. Ten potencjalny klient jest **aktywnym** potencjalnym klientem, w którym udostępniamy informacje o kliencie, który rozpoczął wdrażanie produktu.

   ![Opcja zakupu](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Klient wziął "Test Drive" dla Twojej oferty. Jazdy testowe to przyspieszona okazja do natychmiastowego dzielenia się swoją firmą z potencjalnymi klientami bez żadnych barier wejścia na rynek. Wszystkie dyski testowe wygenerują potencjalnego klienta, który jest zainteresowany wypróbowaniem produktu, aby dowiedzieć się więcej. Dowiedz się więcej o dyskach testowych na [dysku testowym AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Przykład dysku testowego](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Klient wyraża zgodę na udostępnianie swoich informacji po wybraniu opcji "Pobierz teraz". Ten potencjalny klient jest początkowym potencjalnym **klientem,** w którym udostępniamy informacje o kliencie, który wyraża zainteresowanie uzyskaniem produktu. Ołów jest górną częścią lejka akwizycyjnego.

      ![Pobierz teraz opcję](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Klient wybiera "Skontaktuj się ze mną" w swojej ofercie. Ten potencjalny klient jest **aktywnym** liderem, w którym udostępniamy informacje o kliencie, który prosi o kontynuację produktu.

    ![Skontaktuj się ze mną opcja](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Dane potencjalnego klienta
---------

Każdy potencjalny klient otrzymujesz podczas procesu pozyskiwania klientów ma dane w określonych polach. Ponieważ otrzymasz potencjalnych klientów z wielu kroków, najlepszym sposobem obsługi potencjalnych klientów jest dedlikację i personalizację kontynuacji. W ten sposób każdy klient otrzymuje odpowiedni komunikat i tworzysz unikalną relację.

### <a name="lead-source"></a>Źródło ołowiu

Formatem źródła potencjalnego klienta jest **Source**-**Action** |  **Offer**

**Źródła:**"AzureMarketplace", "AzurePortal", "TestDrive" i "AppSource (SPZA)"

**Działania**:
- "INS" -- Instalacja. Ta akcja jest w witrynie Azure Marketplace lub AppSource, gdy klient kupuje produkt.
- "PLT" - Oznacza test prowadzony przez partnera. Ta akcja jest dostępna w udziale appsource, gdy klient używa opcji Skontaktuj się ze mną.
- "DNC" -- Nie kontaktuj się. Ta akcja jest na AppSource, gdy partner, który został krzyżyk na stronie aplikacji zostanie poproszony o kontakt. Udostępniamy informacje, że ten klient został wymieniony w twojej aplikacji, ale nie trzeba się z nimi kontaktować.
- "Utwórz" — ta akcja jest dostępna tylko w witrynie Azure Portal i jest generowana, gdy klient kupuje ofertę na swoim koncie.
- "StartTestDrive" — ta akcja jest dostępna tylko dla dysków testowych i jest generowana, gdy klient rozpoczyna jazdę próbną.

**Oferty**

Poniższe przykłady pokazują unikatowe identyfikatory przypisane do wydawcy i określoną ofertę: checkpoint.check-point-r77-10sg-byol, bitnami.openedxcypress i docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informacje o kliencie

Pola w poniższym przykładzie pokazują informacje o kliencie, które są zawarte w potencjalnym kliencie.
- Imię: John
- Nazwisko: Kowalski
- E-mail:\@jsmith microsoft.com
- Numer telefonu: 1234567890
- Państwo: USA
- Firma: Microsoft
- Tytuł: CTO

>[!Note]
>Nie wszystkie dane w poprzednim przykładzie są zawsze dostępne dla każdego potencjalnego klienta.

Aktywnie pracujemy nad zwiększeniem potencjalnych klientów, więc jeśli istnieje pole danych, którego nie widzisz tutaj, ale chciałbyś mieć, [prześlij nam swoją opinię.](mailto:AzureMarketOnboard@microsoft.com)

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak połączyć system CRM z portalem Cloud Partner Portal
------------------------------------------------------------

Aby rozpocząć pozyskiwanie potencjalnych klientów, stworzyliśmy nasze złącze zarządzania potencjalnymi klientami na portalu Cloud Partner Portal, dzięki czemu można łatwo podłączyć informacje CRM, a my nawiążemy połączenie dla Ciebie. Teraz możesz łatwo wykorzystać potencjalnych klientów generowanych przez rynek bez znaczącego wysiłku inżynieryjnego, aby zintegrować się z systemem zewnętrznym.

![Złącze zarządzania potencjalnymi klientami](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Możemy zapisywać potencjalnych klientów w różnych systemach CRM lub bezpośrednio do tabeli usługi Azure Storage, gdzie można zarządzać potencjalnymi klientami, jak chcesz. Każdy z poniższych linków zawiera instrukcje dotyczące łączenia się z możliwymi głównymi miejscami docelowymi:

-   [Dynamics CRM Online,](./cloud-partner-portal-lead-management-instructions-dynamics.md) aby uzyskać instrukcje dotyczące konfigurowania Dynamics CRM Online do uzyskiwania potencjalnych klientów.
-   [Marketo,](./cloud-partner-portal-lead-management-instructions-marketo.md) aby uzyskać instrukcje dotyczące konfigurowania Konfiguracji Ołowiu Marketo, aby uzyskać potencjalnych klientów.
-    [Salesforce,](./cloud-partner-portal-lead-management-instructions-salesforce.md) aby uzyskać instrukcje dotyczące konfigurowania wystąpienia Salesforce, aby uzyskać potencjalnych klientów.
-    [Tabela platformy Azure,](./cloud-partner-portal-lead-management-instructions-azure-table.md) aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure storage do uzyskiwania potencjalnych klientów w tabeli platformy Azure.
-   [Punkt końcowy https,](./cloud-partner-portal-lead-management-instructions-https.md) aby uzyskać instrukcje konfigurowania punktu końcowego https, aby uzyskać potencjalnych klientów.

Po skonfigurowaniu miejsca docelowego potencjalnego klienta i opublikowaniu oferty firma Wesz ll weryfikować połączenie i wysłać potencjalnego klienta testowego. Podczas przeglądania oferty przed rozpoczęciem pracy na żywo, można również przetestować połączenie potencjalnego klienta, próbując uzyskać ofertę samodzielnie w środowisku podglądu. Ważne jest, aby upewnić się, że ustawienia potencjalnych klientów są aktualne, aby nie utracić potencjalnych klientów, więc upewnij się, że aktualizujesz te połączenia, gdy coś się zmieni na końcu.

<a name="what-next"></a>Co dalej?
----------

Po skonfigurowaniu technicznym, należy włączyć te przewody do bieżącej sprzedaży & strategii marketingowej i procesów operacyjnych. Jesteśmy bardzo zainteresowani lepszym zrozumieniem ogólnego procesu sprzedaży i chcemy ściśle współpracować z Tobą w zakresie dostarczania wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby odnieść sukces. Cieszymy się z Twojej opinii na temat tego, jak możemy zoptymalizować i ulepszyć potencjalnych klientów, które wysyłamy Ci z dodatkowymi danymi, które pomogą ci pomóc tym klientom odnieść sukces. Daj nam znać, jeśli chcesz [przekazać opinie](mailto:AzureMarketOnboard@microsoft.com) i sugestie, aby twój zespół sprzedaży mógł odnieść większy sukces z potencjalnymi klientami marketplace.
