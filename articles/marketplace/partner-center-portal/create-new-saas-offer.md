---
title: Tworzenie nowej oferty SaaS w portalu Marketplace komercyjnych
description: Jak utworzyć nowe oprogramowanie jako usługa (SaaS) oferta ofercie lub sprzedaży w portalu Azure Marketplace usługi AppSource, lub za pośrednictwem programu Cloud Solution Provider (CSP), za pomocą portalu Marketplace komercyjnych na Center partnera firmy Microsoft.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9750aa924ba4b6e4e5f3a51e5b34531d3fab0a6e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243303"
---
# <a name="create-a-new-saas-offer"></a>Tworzenie nowej oferty SaaS

Aby przystąpić do tworzenia oprogramowania jako usługa (SaaS) zapewnia, upewnij się, pierwszy [utworzyć konto w Centrum partnerskim](./create-account.md) , a następnie otwórz [pulpit nawigacyjny handlowe Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), za pomocą **oferuje** wybraną kartą. 

![Komercyjne pulpitu nawigacyjnego portalu Marketplace Centrum partnerskiego](./media/commercial-marketplace-offers.png)

Wybierz pozycję + **Utwórz nową...** przycisk, a następnie wybierz **oprogramowanie jako usługa** elementu menu. 

Po wybraniu jednej z innych typów ofert, nastąpi przekierowanie do starszej wersji [portalu Cloud Partner](https://cloudpartner.azure.com/).  Tylko oferty SaaS są dostępne w portalu Marketplace komercyjnych w Centrum partnerskim w tej chwili. 

![Utwórz okno oferty w Centrum partnerskiego](./media/new-offer.png)


**Nowa oferta** zostanie wyświetlone okno dialogowe. ![Okno dialogowe Nowy oferty](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>Identyfikator oferty i nazwy

- **Identyfikator oferty**: Utwórz unikatowy identyfikator dla każdej oferty w ramach Twojego konta. Ten identyfikator będzie widoczne dla klientów w adresie URL oferty w portalu marketplace i szablonów usługi Azure Resource Manager (jeśli dotyczy). Identyfikator oferty muszą być małe alfanumeryczne (w tym łączników i podkreśleń, ale bez białych znaków). To jest ograniczona do 50 znaków i nie można zaktualizować po wybraniu utworzyć.  
Przykład: test oferty-1
<br>Wynikowa w adresie URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Nazwa oferty**: Oficjalna nazwa oferty aplikacji SaaS, spójność w ramach publikacje, reklamy i witryn sieci web.  Ta nazwa może być na innych.  Oferty, nazwa nie może zawierać spacji, ikon emoji (chyba że są one symbolu znaku towarowego lub praw autorskich) i muszą być ograniczone do 50 znaków.
<br>Przykład: Oferta 1&#8482;

Wybierz pozycję **Utwórz**.  **Oferują Przegląd** strona jest tworzona dla tej oferty.  

![Oferuje Omówienie Centrum partnerskiego](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Przegląd oferty

**Oferują Przegląd** strona zawiera: 

- **Stan publikowania** wyświetla wizualną reprezentację czynności wymagane do publikowania tej oferty i jak długo każdy krok spowoduje przejście do ukończenia. Niekompletne publikowania ikony kroku będą wyszarzone. 

- **Oferują Przegląd** menu zawiera listę linków do wykonywania operacji w ramach tej oferty. Tej listy operacji są różne w zależności od opcji wybranej dla oferty.  
    - Jeśli oferta jest dostępna wersja robocza — Usuń wersję roboczą 
    - Jeśli oferta jest dostępna na żywo — Zatrzymaj sprzedaży oferty 
    - Jeśli ta oferta jest dostępna w wersji zapoznawczej — gotowej do pracy 
    - Jeśli nie wykonano wydawcy Wyloguj — Anuluj publikowanie

## <a name="offer-setup"></a>Ustawienia oferty

**Oferują Instalatora** kartę poprosi o podanie następujących informacji. Wybierz **Zapisz** po wykonaniu tych pól.

- **Czy chcesz sprzedawać za pośrednictwem firmy Microsoft?** (Tak/nie)
    - **Tak**, które chcesz sprzedawać oferty firmy Microsoft z firmą Microsoft hostingu transakcji portalu marketplace w imieniu użytkownika; lub 
    - **Nie**, chcesz po prostu listy oferty za pośrednictwem rynków, przetwarzanie wszystkie transakcje pieniężne, niezależnie od firmy Microsoft.    

### <a name="sell-through-microsoft"></a>Sprzedaży firmy Microsoft

Sprzedaż firmy Microsoft zapewnia większą odnajdywania klienta i przejęcia, pozwala firmie Microsoft na hosta transakcji portalu marketplace w imieniu użytkownika i wykorzystuje funkcje dostępnie handlowych firmy Microsoft.

#### <a name="saas-offer-requirements"></a>Wymagania dotyczące oferty SaaS

Aby wyświetlić listę oprogramowania jako usługi (SaaS) oferuje komercyjnych Portal Marketplace w Centrum partnerskim, muszą być spełnione następujące kryteria:

- Oferty muszą być zgodne z klientami platformy Azure. (Często uzyskać najlepszą wydajność i zgodność aplikacji SaaS również są obsługiwane na platformie Azure, ale nie jest wymagane.) 
- Należy użyć oferty [usługi Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania i zarządzania tożsamościami.
- Należy użyć oferty [interfejsami API usług SaaS realizacji](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2) do integracji z portalu Azure Marketplace.

#### <a name="billing-infrastructure-costs"></a>Rozliczenia kosztów infrastruktury
W przypadku ofert SaaS, jako wydawca, należy uwzględnić opłaty za użycie infrastruktury platformy Azure i opłat licencyjnych jako element pojedynczy koszt. Ten koszt jest przedstawiana jako płaskie miesięcznej opłaty za do klienta. Użycie infrastruktury platformy Azure zarządzane i rozliczane w przypadku partnera, bezpośrednio. Opłaty za użycie rzeczywistej infrastruktury nie są widoczne dla klienta. Wydawcy zazwyczaj zdecydować się na pakietu opłaty za użycie infrastruktury platformy Azure do ich ceny licencji oprogramowania. 

Oprogramowanie opłat licencyjnych są uporządkowane jako opłata stosowana jest stała stawka miesięczna, cyklicznego subskrypcji oparty na lokacji i nie są mierzone lub oparty na zużyciu.

|**Koszt licencji**|**100 USD miesięcznie**|
|:---|:---|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)|Rozliczane bezpośrednio z wydawcą zostało nawiązane nie klienta|
|Rozliczenie przez firmę Microsoft|100,00 USD miesięcznie (wydawcy muszą uwzględniać żadnych kosztów infrastruktury poniesionych lub przekazywania w opłacie licencyjnej)|

- W tym scenariuszu firma Microsoft rozlicza 100,00 USD dla licencji na oprogramowanie i płaci się $80,00 z wydawcą zostało nawiązane.
- Partnerzy, którzy mają kwalifikowanych dla **obniżona za usługę portalu Marketplace** zobaczą opłaty mniejsze transakcji na SaaS oferuje od maja 2019 aż do 2020 czerwca. W tym scenariuszu firma Microsoft rozlicza 100,00 USD dla licencji na oprogramowanie i płaci się $90.00 z wydawcą zostało nawiązane.

> [!NOTE]
> **Zmniejszona opłata za usługę portalu Marketplace**: W przypadku niektórych SaaS oferuje czy zostały opublikowane na rynku komercyjnych, Microsoft zmniejsza jego opłata za usługę portalu Marketplace z 20% (zgodnie z opisem w umowie wydawcy portalu Microsoft) do 10%. Aby oferty kwalifikują się co najmniej jedno z Twoich ofert musi zostały wyznaczone przez firmę Microsoft jako gotowości do wspólnej sprzedaży adresu IP lub adresów IP wspólnej odsprzedaży uszeregowane według priorytetów.  Uprawnienia muszą być spełnione co najmniej pięciu (5) dni przed końcem dnia każdego miesiąca kalendarzowego w celu odbierania to zmniejszenie opłata za usługę portalu Marketplace na miesiąc.  Zmniejszona za usługę portalu Marketplace nie ma zastosowania do maszyn wirtualnych, aplikacji zarządzanych przez lub innych produktów, udostępniane za pośrednictwem rynku komercyjnych.  Zmniejszona za usługę portalu Marketplace będzie dostępna jedynie kwalifikowaną oferty na potrzeby opłat licencyjnych za zbieranych przez firmę Microsoft, między 1 maja 2019 a 30 czerwca 2020.  Po upływie tego czasu opłaty za usługę portalu Marketplace powróci do jego normalnej kwoty. 

|**W ramach firmy Microsoft**|**100 USD miesięcznie**|
|:---|:---|
|Microsoft płaci 80% kosztów licencji <br>**Dla aplikacji SaaS kwalifikowaną Microsoft płaci 90% firm z kosztami licencji*|$80,00 na miesiąc <br>*$* 90.00 na miesiąc *|


#### <a name="csp-program-opt-in"></a>Dostawcy usług Kryptograficznych programu wymaga zgody na uczestnictwo
[Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program umożliwia oferty dotyczące oprogramowania można uruchamiać na milionach kwalifikowaną klientów firmy Microsoft, przy minimalnym inwestycji sprzedaży i marketingu.

- **Kanały: Udostępnij Moje oferty w programie CSP** (pole)

Wybranie w celu udostępnienia oferty w programie CSP umożliwia dostawców rozwiązań w chmurze do sprzedaży swojego produktu w ramach rozwiązania powiązane swoim klientom. 

### <a name="list-through-microsoft"></a>Lista firmy Microsoft

Promuj swoją firmę z firmą Microsoft, tworząc listę portalu marketplace. Wybierając na liście tylko oferty i nie przeprowadzaj transakcje za pośrednictwem firmy Microsoft oznacza, że Microsoft nie uczestniczy bezpośrednio w transakcjach licencji oprogramowania. Nie ma skojarzonej transakcji opłat i wydawca zapewnia 100% oprogramowanie licencjonowania opłaty zebrane z klienta. Wydawca jest jednak odpowiedzialni za obsługę wszystkich aspektów transakcji licencji oprogramowania, w tym między innymi: kolejność realizacji, pomiaru użytkowania, rozliczeniami, Fakturowanie, płatności i kolekcji. 

- **Jak chcesz potencjalnym klientom interakcję z tej oferty listy?**

##### <a name="get-it-now-free"></a>Pobierz teraz (bezpłatnie)
Lista oferty dla klientów za darmo, podając prawidłowy adres URL (rozpoczynający się od http lub https) gdzie mają dostęp do aplikacji.  Na przykład: `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Bezpłatna wersja próbna
Wyświetl listę oferty dla klientów na podstawie bezpłatnej wersji próbnej, podając prawidłowy adres URL (rozpoczynający się od http lub https) gdzie mają dostęp do aplikacji.  Na przykład: `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Kontakt ze mną
Zbieraj informacje kontaktowe klienta, nawiązując połączenie z system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o uprawnienia, aby udostępnić swoje informacje. Te szczegóły klienta wraz z nazwa oferty, identyfikator i źródło marketplace wykryto do oferty, będą wysyłane do systemu CRM, które zostały skonfigurowane. Aby uzyskać więcej informacji o konfigurowaniu CRM, zobacz [Connect — Zarządzanie potencjalnymi klientami](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Przykład portalu marketplace oferty listy

![Przykład ofert portalu marketplace oraz oznaczenia](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Włączanie wersji testowej

Wersji testowej jest doskonałym sposobem na pokazują ofertę potencjalnym klientom, co daje im możliwość "try przed zakupem", i zwiększonej konwersji i generowanie potencjalnych klientów wysoko wykwalifikowanych. [Dowiedz się więcej na temat wersje testowe.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Włącz wersję testową** (checkbox) 

Po włączeniu wersji testowej zostanie prośba do konfigurowania środowiska pokaz dla klientów wypróbować ofertę w ustalonym czasie. 

### <a name="type-of-test-drive"></a>Typ wersji testowej

- **[Usługa Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, wchodzące w skład rozwiązania. Produkty, które mieszczą się w tym scenariuszu korzystać tylko zasobów platformy Azure.
- **[Dynamics 365 dla firm siedziby](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Firma Microsoft hostuje i przechowuje usługa dysku testowego (w tym obsługi administracyjnej i wdrażania) for Business Central, system do planowania zasobów przedsiębiorstwa (Finanse i operacje, łańcuchu dostaw, CRM, itp.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Firma Microsoft obsługuje i obsługuje usługa dysku testowego (w tym obsługi administracyjnej i wdrażania) dla systemu zaangażowania użytkowników (sprzedaż, usługi, usługi programu project, usługi terenowe itp.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Firma Microsoft obsługuje i przechowuje usługa dysku testowego (w tym obsługi administracyjnej i wdrażania) for Finance and Operations do planowania zasobów przedsiębiorstwa systemu (finanse, operacje, produkcji, łańcuch dostaw, itp.). 
- **[Aplikacja logiki](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Szablon wdrożenia obejmujący wszystkie architektury złożonych rozwiązań. Wszystkie produkty niestandardowego należy używać tego rodzaju wersji testowej.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Osadzonego linku do niestandardowej pulpitu nawigacyjnego. Produkty, które pokazują, że Interaktywne wizualizacje usługi Power BI należy używać tego rodzaju wersji testowej. Wszystko, co należy przekazać w tym miejscu jest osadzony adres URL do usługi Power BI.

#### <a name="additional-test-drive-resources"></a>Zasoby dysków dodatkowych testów
- [Test stacji techniczne najlepszych rozwiązań](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Przetestuj najlepsze rozwiązania marketingowe](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Testowanie pagera jeden przegląd dysku](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Connect — Zarządzanie potencjalnymi klientami

Bezpośrednie nawiązywanie połączenia z klientami przy sporządzanie list oferty rynków i Podłączanie systemu zarządzania relacjami z klientami (CRM), dzięki czemu mogą odbierać informacje kontaktowe klienta, natychmiast po klient wyraża zainteresowania lub wdraża usługi produkt.

- **Wybierz miejsce docelowe potencjalny klient** (menu rozwijane): Podaj szczegóły połączenia z systemem CRM, gdzie chcesz nam wysyłanie potencjalnych klientów. 

Centrum partnerskie obsługuje następujące systemy CRM dla — Zarządzanie potencjalnymi klientami. Wybierz link, aby uzyskać instrukcje dotyczące konfiguracji.

- Usługa Azure Blob — Podaj kontaktowy adres e-mail, nazwa kontenera i parametry połączenia konta magazynu. 
- [Usługa Azure Table](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) — Podaj kontaktowy adres e-mail i parametry połączenia konta magazynu. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) — Podaj kontaktowy adres e-mail, adres URL i tryb uwierzytelniania (Office 365 lub Azure Active Directory).
- [Punkt końcowy HTTPS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) — Podaj kontaktowy adres e-mail i adres URL punktu końcowego protokołu HTTPS. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) — Podaj kontaktowy adres e-mail, identyfikator formularza, Munchkin identyfikator konta i identyfikator serwera.
- [SalesForce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) — Podaj kontaktowy adres e-mail i identyfikatora organizacji. 

#### <a name="additional-lead-management-resources"></a>Dodatkowe potencjalnego klienta zarządzania zasobami
- [Prowadzić zarządzania — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnego klienta](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Prowadzić pagera jeden Przegląd zarządzania](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Pamiętaj, aby **Zapisz** przed przejściem do następnej sekcji!

## <a name="properties"></a>Właściwości
**Właściwości** kartę pyta, można zdefiniować kategorie i branż, używane do grupowania Twoją ofertę w rynków umów obsługi oferty i wersji aplikacji. 

Wybierz **Zapisz** po wykonaniu tych pól. 

### <a name="category"></a>Category
Wybierz co najmniej jeden (1) i maksymalnie trzech (3) kategorie używane do grupowania oferty w obszarach wyszukiwania odpowiedniej witryny marketplace. Skontaktuj się, jak Twoja oferta obsługuje te kategorie w opisie oferty. 

### <a name="industry"></a>Branża
Wybierz maksymalnie dwóch (2) branżach używane do grupowania oferty w obszarach wyszukiwania odpowiedniej witryny marketplace. Jeśli Twoja oferta nie jest specyficzne dla branży, nie należy wybierać jedną. Skontaktuj się, jak Twoja oferta obsługuje wybrane branże w opisie oferty. 

### <a name="app-version"></a>Wersja aplikacji
To pole jest opcjonalne używany w witrynie marketplace usługi AppSource do identyfikowania numer wersji oferty. 

### <a name="standard-contract"></a>Kontrakt standardowy

- **Użyj standardowej umowy?**

Aby uprościć proces zakupów dla klientów i ograniczyć złożoność prawne dla dostawców oprogramowania, firma Microsoft oferuje szablon standardowej umowy w celu ułatwienia transakcji w portalu marketplace. 

Zamiast tworzenia niestandardowych warunków i postanowień, wydawcom portalu Azure Marketplace można oferować swoje oprogramowanie w ramach standardowej umowy, klienci muszą tylko Zweryfikuj i akceptują jeden raz. 

Standardowa kontrakt można znaleźć tutaj: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Warunki użytkowania

Jeśli Twoje postanowienia licencyjne różnią się od standardowej umowy, można wybrać prawne warunki użytkowania, w tym miejscu należy wprowadzić. Można również wprowadzić maksymalnie 10 000 znaków tekstu, w tym polu. Jeśli warunki użytkowania wymaga dłuższego opis, wprowadź pojedynczy link z adresem URL, w tym polu, gdzie można znaleźć usługi dodatkowe postanowienia licencyjne. Ona klientom zostanie wyświetlona jako aktywne połączenie.

Klienci są proszeni o zaakceptowanie tych warunków, zanim użytkownik podejmie próbę aplikacji. 

Pamiętaj, aby **Zapisz** przed przejściem do następnej sekcji!

## <a name="offer-listing"></a>Oferuje listę

Oferty Wyświetla kartę języków (i rynków), których Twoja oferta jest dostępna, obecnie angielski (Stany Zjednoczone) jest jedyną lokalizacją dostępne. Ponadto ta strona wyświetla stan listy specyficzny dla języka i daty/godziny, który został dodany. Musisz zdefiniować szczegóły portalu marketplace (oferują nazwę, opis, wyszukiwane terminy, itp.) dla każdego z języków / na rynek.

### <a name="offer-listings"></a>Ofert

Podaj szczegóły, które mają być wyświetlane w portalu marketplace, w tym opis oferty i zasobów marketingowych.

- **Nazwa** (wymagane): Nazwy zdefiniowane w tym miejscu będzie wyświetlany jako tytuł oferty w Twojej witrynie marketplace(s), które zostały wybrane. Nazwy są wstępnie wypełnione w oparciu poprzedniego **nowa oferta** wpisu.  Może to być innych.  To nie mogą zawierać spacji, ikon emoji (chyba że są one symbole znaków towarowych i prawach autorskich) i muszą być ograniczone do 50 znaków.
- **Podsumowanie** (wymagane): Podaj krótki opis oferty do użycia w wynikach wyszukiwania aukcji portalu marketplace. W tym polu można wprowadzić do 100 znaków tekstu.
- **Opis** (wymagane): Podaj opis oferty mają być wyświetlane w portalu marketplace Przegląd aukcji. Należy wziąć pod uwagę, w tym korzyści, kluczowych korzyści, wszystkie skojarzenia kategorii lub branży możliwości zakupu w aplikacji, wszystkie wymagane informacje i łącza, aby dowiedzieć się więcej.
W tym polu można wprowadzić maksymalnie 3000 znaków tekstu. Aby uzyskać dodatkowe wskazówki, zobacz artykuł [wpisać opis wspaniałej aplikacji](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Wyszukiwanie słów kluczowych**: Wprowadź maksymalnie trzech słów kluczowych, używanych przez klientów można znaleźć oferty w marketplace(s).
- **Instrukcje z wprowadzeniem** (wymagane): Wyjaśniono, jak skonfigurować i rozpocząć korzystanie z aplikacji dla potencjalnych klientów.  Ten przewodnik Szybki Start mogą zawierać łącza do bardziej szczegółowa dokumentacja online. W tym polu można wprowadzić maksymalnie 3000 znaków tekstu. 

#### <a name="links"></a>Linki

- **Zasady zachowania poufności informacji** (wymagane): Połącz z zasady zachowania poufności informacji w organizacji. Jesteś odpowiedzialny za zapewnienie, że aplikacja jest zgodna z prawem do prywatności i podając prawidłową poufności
- **Program CSP, materiałów marketingowych** (opcjonalnie): Należy podać link do materiałów marketingowych, jeśli chcesz rozszerzyć oferty [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program. Dostawcy usług Kryptograficznych rozszerza oferty do większego zakresu uprawnionych klientów, włączanie partnerzy programu CSP pakietu na rynku, i sprzedawaj swoją ofertę. Te odsprzedawców potrzebny dostęp do materiałów marketingowych oferty. Aby uzyskać więcej informacji, zobacz [usług Go-To-Market](https://partner.microsoft.com/reach-customers/gtm).
- **Przydatne linki** (opcjonalnie): Opcjonalne dodatkowe dokumentów online dotyczących aplikacji lub powiązanych usług na liście, zapewniając **tytuł** i **adresu URL**. Dodaj dodatkowe przydatne linki, klikając przycisk **+ Dodaj adres URL**.

#### <a name="contact-information"></a>Informacje kontaktowe

- **Kontakty**: Dla każdego kontaktu klienta należy podać pracownikowi **nazwa** , **numer telefonu**, i **E-mail** adresu.  (Te *nie będzie* być wyświetlane publicznie). A **adres URL pomocy technicznej** jest również wymagane dla **pomocy technicznej, skontaktuj się z pomocą** grupy.  (Te informacje *będzie* być wyświetlane publicznie).

**Obsługuje kontakt** (wymagane): Ogólne pytań dotyczących pomocy technicznej.

**Inżynierów skontaktuj się z pomocą** (wymagane): W przypadku pytań technicznych.

**Kanał Manager contact** (wymagane): W razie pytań odsprzedawcy związane z programu CSP.

#### <a name="files-and-images"></a>Pliki i obrazy

- **Dokumenty** (wymagane): Dodaj powiązanych dokumentów marketingu oferty, w formacie PDF, zapewniając co najmniej jeden (1) i maksymalnie trzech (3) dokumenty na ofertę.
- **Obrazy** (opcjonalnie): Istnieje wiele miejsc, w której obrazów logo swojej oferty mogą być wyświetlane w całym marketplace(s), wymaga następujących rozmiarów — mała liczba godzin: 48 x 48 pikseli _(wymagane) i_ nośnika: 90 x 90 pikseli, duże: piksele 216 x 216 _(wymagane) i_ szerokości: 255 x 115 pikseli i Hero: 815 x 290 pikseli. Wszystkie obrazy muszą być w. PNG format.
- **Zrzuty ekranu** (wymagane): Dodaj zrzuty ekranu, demonstrując oferty. Maksymalnie pięć (5), zrzuty ekranu mogą być dodawane i z tego względu o rozmiarze 1280 x 720 pikseli. Wszystkie obrazy muszą być w. PNG format.
- **Filmy wideo** (opcjonalnie): Dodać łącza do filmów wideo, demonstrując oferty. Możesz użyć linków do serwisu YouTube lub Vimeo filmów wideo, które są wyświetlane wraz z ofertą dla klientów. Należy również wprowadzić obraz miniatury wideo o rozmiarze 1280 x 720 pikseli w formacie PNG. Można wyświetlić maksymalnie cztery filmów wideo na ofertę.

Pamiętaj, aby **Zapisz** przed przejściem do następnej sekcji!

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe witryny marketplace listę zasobów

- [Najlepsze rozwiązania dotyczące portalu marketplace oferty list](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Wersja zapoznawcza

**Podgląd** karta pozwala na zdefiniowanie ograniczonego **odbiorców wersji zapoznawczej** służącą do zwalniania oferty przed opublikowaniem oferty na żywo do szerszego grona odbiorców witryny marketplace.

> [!IMPORTANT]
> Musisz wybrać **emisji na żywo** przed oferty zostaną opublikowane na żywo do odbiorców publicznej witryny marketplace za Twoją ofertę w wersji zapoznawczej.

- **Zdefiniuj odbiorców (wersja zapoznawcza): Dodaj pojedynczy adres e-mail konta usługi AAD/MSA, dla każdego wiersza, oraz opcjonalny opis.**

Ręcznie dodaj adresy e-mail maksymalnie dziesięciu (10) lub dwadzieścia (20) w przypadku przekazywania pliku CSV dla istniejącego konta Microsoft (MSA) lub konta usługi Azure Active Directory (AAD), ułatwiające wykonywanie sprawdzania poprawności oferty przed opublikowaniem na żywo. Dodanie tych kont, są definiowane na odbiorców, którzy będą mieć dostępu do wersji zapoznawczej oferty przed opublikowaniem marketplace(s). Jeśli oferta usługi jest już na żywo, mogą nadal definiować odbiorców (wersja zapoznawcza) do testowania, zmiany lub aktualizacje do swojej oferty.

> [!NOTE]
> Grupy odbiorców (wersja zapoznawcza) różni się od prywatnych odbiorców. Odbiorcy (wersja zapoznawcza) ma mieć dostęp do swojej oferty _wcześniejsze_ są publikowane na żywo w rynków. Można również utworzyć plan i był dostępny tylko dla prywatnych odbiorców. W **planowanie listy** karcie można zdefiniować prywatny odbiorców za pomocą **jest prywatny plan** pola wyboru. Następnie można zdefiniować prywatny odbiorców do 20 000 klientów przy użyciu identyfikatorów dzierżawy usługi Azure.

## <a name="technical-configuration"></a>Pomoc konfiguracji

**Konfiguracji technicznej** kartę definiuje szczegóły techniczne (Ścieżka adresu URL elementu webhook, identyfikator dzierżawy i identyfikator aplikacji) używany do łączenia z ofertą. To połączenie umożliwia nam aprowizować ofertę jako zasoby w subskrypcji platformy Azure przez klienta, jeśli zdecydują się je nabyć.

- **Adres URL strony** (wymagane): Zdefiniuj lokacji adres URL, którego klienci będą kierowane do proponowany po nabycia oferty w portalu Marketplace. Ten adres URL będzie również punkt końcowy, który będzie otrzymywać połączenia interfejsy API w celu ułatwienia handlowych firmy Microsoft.

- **Element webhook połączenia** (wymagane): Dla wszystkich zdarzeń asynchronicznych, które firma Microsoft musi wysłać użytkownikom w imieniu klienta (przykład: Subskrypcja platformy Azure stała się nieprawidłowa), wymagamy, musisz podać element webhook połączenia. Jeśli nie masz jeszcze system elementu webhook w miejscu, najprostsza konfiguracja ma mieć aplikacja logiki punktu końcowego HTTP, który będzie nasłuchiwać zdarzeń ogłaszany do niego i odpowiednio je obsłużyć (np. https:\//prod-1westus.logic.azure.com:443/work). Aby uzyskać więcej informacji, zobacz [wywołania wyzwalacza lub zagnieżdżanie przepływy pracy za pomocą punktów końcowych HTTP w usłudze logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Identyfikator dzierżawy usługi Azure AD** (wymagane): W witrynie Azure portal, możemy wymagać, aby użytkownik [Utwórz aplikację usługi Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , dzięki czemu możemy sprawdzić poprawność połączenia między naszych dwóch usług znajduje się za uwierzytelnianiu komunikacji. Aby znaleźć [identyfikator dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), przejdź do usługi Azure Active Directory i wybierz **właściwości**, poszukaj **identyfikator katalogu** numer na liście (np. 50c464d3-4930-494c-963c-1e951d15360e).

- **Identyfikator aplikacji w usłudze Azure AD** (wymagane): Należy również swoje [identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) i klucza uwierzytelniania. Aby uzyskać te wartości, przejdź do usługi Azure Active Directory, a następnie wybierz **rejestracje aplikacji**, poszukaj **identyfikator aplikacji** numer na liście (np. 50c464d3-4930-494c-963c-1e951d15360e). Aby znaleźć klucz uwierzytelniania, przejdź do **ustawienia** i wybierz **klucze**. Należy podać opis i czas trwania, a następnie będzie należy podać wartość liczbową.

 Należy pamiętać, że identyfikator aplikacji platformy Azure skojarzony z Identyfikatora wydawcy, dlatego upewnij się, że ten sam identyfikator aplikacji jest używany w Twoich ofert.

## <a name="plan-overview"></a>Omówienie planu

**Omówienie planu** karta umożliwia obejmują różnorodne opcje planu w ramach tej samej oferty. Te plany (czasami określane jako jednostki SKU) mogą być różne pod względem wersji, zysków z aplikacji lub warstwy usługi. Należy zdefiniować co najmniej jeden plan, aby sprzedawać swoją ofertę w portalu marketplace.

Po utworzeniu zobaczysz nazwy planu, identyfikatory, cennik modeli, dostępności (publiczny lub prywatny), bieżący, publikowania, stan i wszystkie dostępne akcje.

**Akcje** dostępne w **omówienie planu** różnią się w zależności od bieżącego stanu planu i mogą obejmować:

- Jeśli stan planu **projekt** — Usuń wersję roboczą
- Jeśli stan planu **Live** — Stop sprzedawać, plan lub odbiorców prywatnej synchronizacji

**Utwórz nowy plan** (co najmniej jeden plan dla osób, które umożliwia sprzedaży firmy Microsoft)

- **Identyfikator planu:** Utwórz identyfikator unikatowy plan dla każdego planu w ramach tej oferty. Ten identyfikator będzie widoczne dla klientów w product adresu URL i usługi Azure Resource Manager template (jeśli dotyczy). Użyj tylko małych znaków alfanumerycznych kreski i znaki podkreślenia. Maksymalnie 50 znaków są dozwolone dla tego identyfikatora planu. Należy pamiętać, że identyfikator nie można zmodyfikować po utworzeniu wybranie.
- **Nazwa planu:** Odbiorcy zobaczą tę nazwę podczas podejmowania decyzji o który plan wybrać w ramach oferty. Utwórz nazwę oferty unikatowy dla każdego planu w ramach tej oferty. Nazwa planu jest używany do odróżnienia plany oprogramowania, które mogą być częścią tej samej oferty (np. Nazwa oferty: Windows Server; planów: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Lista planu

**Planowanie listy** karta zawiera języki (i rynków), gdzie Twój plan jest dostępny, obecnie angielski (Stany Zjednoczone) jest jedyną lokalizacją dostępne. Ponadto ta strona wyświetla stan listy specyficzny dla języka i daty/godziny, który został dodany. Musisz zdefiniować szczegóły portalu marketplace (oferują nazwę, opis, wyszukiwane terminy, itp.) dla każdego z języków / na rynek.

#### <a name="plan-listing-details"></a>Plan szczegółowe informacje dotyczące listy

Wybranie jednego z języków plan spowoduje wyświetlenie **planowanie listy** informacji, w tym **nazwa** i **opis.**

- **Nazwa**: Wstępne wypełnianie oparte na wersji zapoznawczej **nowy plan** wejścia i będzie wyświetlany jako tytuł oferty "oprogramowania planu" wyświetlana w portalu marketplace.
- **Opis:** Ten opis jest wyjaśnienie, co sprawia, że ten plan oprogramowania unikatowy i różnice z innych planów oprogramowania w ramach oferty. Może zawierać więcej niż 500 znaków.

Wybierz **Zapisz** po wykonaniu tych pól.

#### <a name="plan-pricing-and-availability"></a>Ceny planu i dostępności

**Ceny i dostępności** karta pozwala na skonfigurowanie rynków, na których ten plan będzie dostępny w modelu żądaną zysków, ceny i okresu rozliczeniowego. Ponadto można wskazać, czy plan widoczne dla wszystkich użytkowników lub tylko do określonych odbiorców (odbiorców prywatnych).

#### <a name="markets"></a>Rynki

- **Edytuj rynków** (opcjonalnie)

Każdy plan musi być dostępny w co najmniej jednym rynku. Zaznacz pole wyboru dla dowolnej lokalizacji na rynku, w którym chcesz udostępnić ten plan. Pole wyszukiwania i przycisk wybierania "Przekazana podatku" krajach, w których Microsoft przekazuje sprzedaży i Użyj podatku w Twoim imieniu uwzględniono pomocy. 


Jeśli ustawiono już ceny dla planu dolary USA (USD) i Dodaj inną lokalizację na rynku ceny na nowe rynki będą obliczane zgodnie z bieżącym kursy wymiany. Należy zawsze przejrzeć ceny dla poszczególnych rynków przed opublikowaniem. Ceny można wyświetlić, korzystając z linku "Eksportuj ceny (xlsx)" po zapisaniu zmian.

#### <a name="pricing"></a>Cennik

- **Model cen**: Stosowana jest stała stawka lub stanowisko w warstwie na podstawie

**Stosowana jest stała stawka:** Zapewnianie dostępu do oferty z ceną stosowana jest stała stawka cenie miesięcznej lub rocznej. To jest czasami nazywane cen oparty na lokacji.

**Stanowisko w warstwie na podstawie:** Zapewnianie dostępu do oferty z ceną na podstawie liczby użytkowników, uzyskiwanie dostępu do oferty lub zajmuje *stanowiska*. Ten model oparty na stanowisko umożliwia ustawienie minimalna i maksymalna liczba dozwolonych stanowisk na podstawie ceny. Dzięki temu inna cena punkty można skonfigurować na podstawie liczby użytkowników, konfigurując wiele planów.  Te pola są opcjonalne. Jeśli pole pozostanie puste, liczbę stanowisk będą interpretowane jako niemający limit (minimum 1) i maksymalnie tyle, ponieważ system może obsłużyć. Te pola może być edytowana w ramach aktualizacji do planu.

Po opublikowaniu rozliczeń wybór modelu cen nie można zmienić. Ponadto wszystkie plany dla tej samej oferty muszą współużytkować ten sam model cen.

- **Termin rozliczeń**: Miesięcznej lub rocznej

Wybierz częstotliwość, z którą klienci muszą zapłacić tę cenę, na liście. Należy podać co najmniej jednej miesięcznej lub rocznej cen lub obie opcje mogą być udostępniane klientom.

- **Cena**: USD miesięcznie lub USD rocznie

Ceny zestawu w walucie lokalnej (USD = dolar amerykański) są konwertowane na lokalnej walucie rynków wszystkie zaznaczone, przy użyciu bieżących kursów wymiany dostępne podczas instalacji. Sprawdź ceny przed opublikowaniem, eksportując ceny arkusza kalkulacyjnego i przeglądając cena w poszczególnych segmentach rynku. Jeśli chcesz skonfigurować niestandardowe ceny rynku poszczególnych zmodyfikowaniu i zaimportowaniu cen arkusza kalkulacyjnego. Są odpowiedzialne za sprawdzanie poprawności tej ceny i właścicielem tych ustawień.
**Należy najpierw zapisać zmiany cennika, aby włączyć eksportowanie danych dotyczących cen.*

Przejrzyj ceny dokładnie przed opublikowaniem, ponieważ istnieją pewne ograniczenia, o czym można zmienić po opublikowaniu planu:

- Po opublikowaniu planu nie można zmienić modelu cen.
- Po opublikowaniu okresu rozliczeniowego dla danego planu nie można usunąć później.
- Po opublikowaniu ceny na rynek w planie nie można zmienić później.

### <a name="plan-audience"></a>Planowanie odbiorców

Masz możliwość skonfigurowania każdego planu był widoczny dla wszystkich użytkowników lub tylko dla określonej grupy odbiorców wybrane. Możesz przypisać członkostwo w tej grupie odbiorców ograniczone, przy użyciu identyfikatora dzierżawy usługi Azure AD.

#### <a name="privacy"></a>Ochrona prywatności

- **Jest to prywatne plan** (opcjonalne pole wyboru)

Zaznacz to pole do planu prywatne i widoczne tylko ograniczony odbiorców wybrane. Po opublikowaniu jako prywatne plan można zaktualizować odbiorców lub chce udostępnić plan wszystkim użytkownikom. Po opublikowaniu planu jako widoczny dla wszystkich musi pozostać widoczne dla wszystkich użytkowników. (Planu nie można skonfigurować jako prywatne plan ponownie).

- **Ograniczone odbiorców (identyfikatory dzierżawy)**

Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnych. Przypisywany jest dostęp za pomocą identyfikatorów dzierżawy z opcją zawiera opis każdego Identyfikatora dzierżawy przypisany. Można dodać maksymalnie 10 dzierżawy identyfikatorów lub 20 000 klientów. identyfikatory dzierżawy, jeśli zaimportowanie pliku CSV arkusza kalkulacyjnego.

Dzierżawa to reprezentacja organizacji, identyfikator reprezentowany jako identyfikatora GUID (Globally Unique Identifier, numer 128-bitową liczbę całkowitą, używany do identyfikowania zasobów). Jest to dedykowane wystąpienie usługi Azure AD, którą organizacja lub deweloper aplikacji otrzymuje po utworzeniu relacji z firmą Microsoft, na przykład zarejestrowaniu się na platformie Azure, w usłudze Microsoft Intune lub Microsoft 365. Każda dzierżawa usługi Azure AD jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD. Aby sprawdzić dzierżawę, zaloguj się do witryny Azure portal przy użyciu konta, którego chcesz użyć do zarządzania aplikacją. Jeśli masz dzierżawę, nastąpi automatyczne zalogowanie do niej, a nazwa dzierżawy zostanie wyświetlona bezpośrednio pod nazwą Twojego konta. Umieść wskaźnik myszy na nazwie konta w prawym górnym rogu witryny Azure Portal, aby wyświetlić swoją nazwę, adres e-mail, identyfikator katalogu/dzierżawy (GUID) oraz domenę. Jeśli Twoje konto jest skojarzone z wieloma dzierżawami, możesz wybrać nazwę swojego konta, aby otworzyć menu, w którym można przełączać się między dzierżawami. Każda dzierżawa ma własny identyfikator dzierżawy. Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu URL nazwę domeny w: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

Ofert SaaS użyć identyfikatorów dzierżawy, aby zdefiniować prywatny odbiorców, identyfikatory subskrypcji platformy Azure (który również jest reprezentowanych jako identyfikatory GUID) mogą używać innych typów ofert.

> [!NOTE]
> Prywatne odbiorców (lub ograniczone odbiorców) różni się od odbiorców wersji zapoznawczej. W **[Podgląd](#preview)** karcie można zdefiniować odbiorców wersji zapoznawczej. Odbiorcy (wersja zapoznawcza) ma mieć dostęp do swojej oferty *wcześniejsze* do oferty opublikowanej na żywo w witrynie marketplace. Natomiast określenie odbiorców prywatnych ma zastosowanie tylko do określonego planu, (wersja zapoznawcza) są wyświetlane wszystkie plany (prywatnej lub nie), ale tylko w okresie ograniczonej wersji zapoznawczej, gdy plan jest przetestowany i zweryfikowany.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Lista planów w ramach oferty w portalu marketplace

![W planie rynku przykładzie lista z uwagi](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Wersja testowa

**Przetestuj** karta umożliwia konfigurowanie pokaz (lub "wersja testowa") która umożliwi klientom próby przed zatwierdzeniem oferty do zakupu. Dowiedz się więcej w artykule [co to jest wersja testowa?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Jeśli nie chcesz zapewnić wersji testowej oferty, wróć do **[oferują instalacji](#offer-setup)** strony, a następnie usuń zaznaczenie pola wyboru **wersji testowej Włącz**.

### <a name="technical-configuration"></a>Pomoc konfiguracji
Dostępne są następujące typy dysków testu o własne wymagania dotyczące konfiguracji Technical Preview.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikacja logiki](#technical-configuration-for-logic-app-test-drive)
- [Usługa Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (niewymagane w konfiguracji technicznej)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Testowanie konfiguracji technicznej dla usługi Azure Resource Manager

Szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, wchodzące w skład rozwiązania. Produkty, które mieszczą się w tym scenariuszu korzystać tylko zasobów platformy Azure. Dowiedz się więcej o konfigurowaniu [wersji testowej usługi Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiony** (wymagane): Obecnie istnieją 26 regionach obsługiwanej przez platformę Azure, której wersji testowej mogą być udostępniane. Zazwyczaj chcesz udostępnić wersji testowej w regionach, w których spodziewasz się, jak największej liczbie klientów, tak aby użytkownik może wybrać najbliższego regionu w celu uzyskania najlepszej wydajności. Należy się upewnić, że Twoja subskrypcja może być wdrażane są wszystkie zasoby wymagane we wszystkich regionach, który wybierasz.

- **Wystąpienia**: Wybierz typ (gorąca lub zimno) i liczba dostępnych wystąpień, które będą pomnożona przez liczbę regionów, w których Twoja oferta jest dostępna.

**Gorąca**: Ten typ wystąpienia jest wdrożone i Oczekiwanie na dostęp do wybranego regionu. Klienci mogą zostać natychmiast uzyskać dostęp *gorąca* wystąpień wersję testową, a nie musisz czekać, aż do wdrożenia. Jego wadą jest to, że te wystąpienia są zawsze uruchamiane na Twojej subskrypcji platformy Azure, spowoduje naliczenie opłaty za większych przestojów, koszt. Zdecydowanie zaleca się mieć co najmniej jedną *gorąca* wystąpienia, ponieważ większość klientów nie chcesz czekać do pełnego wdrożenia skutkuje nadania w użycia przez klientów, jeśli nie *gorąca* wystąpienie jest dostępne.

**Zimnych**: Ten typ wystąpienia przedstawia całkowita liczba wystąpień, które prawdopodobnie mogą być wdrażane na region. Wystąpienia zimnych wymagają cały Test dysku szablonu Resource Manager do wdrożenia, gdy klient zażąda wersji testowej, więc *zimnych* wystąpienia są znacznie wolniejsze ładowanie niż *gorąca* wystąpień. Kosztem jest, że masz płacisz za czas trwania wersji testowej, jest *nie* zawsze uruchamiane na Twojej subskrypcji platformy Azure jako *gorąca* wystąpienia.

- **Testuj szablon usługi Azure Resource Manager dysku**: Przekaż plik zip zawierający szablon usługi Azure Resource Manager.  Dowiedz się więcej na temat tworzenia szablonu usługi Azure Resource Manager w artykule przewodnika Szybki Start [tworzenia i wdrażania szablonów usługi Azure Resource Manager przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Czas trwania wersji testowej** (wymagane): Określa długość czasu, przez jaki wersji testowej pozostaną aktywne w liczba godzin. Wersji testowej kończy się automatycznie po zakończeniu tego okresu. Ten czas trwania może tylko pasujących zestaw przez całkowitą liczbę godzin (np. godziny "2", "1.5" jest nieprawidłowy).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Wersja testowa techniczne configuration for Dynamics 365

Firma Microsoft można usunąć złożoność Konfigurowanie wersji testowej, udostępniania i utrzymywania usługi aprowizacji i wdrażania za pomocą tego rodzaju wersji testowej. Konfigurację dla tego typu hostowanej testowej jest taki sam, niezależnie od tego, czy wersji testowej jest przeznaczony dla odbiorców Business Central, zaangażowania użytkowników lub operacje.

- **Maksymalna liczba współbieżnych testu dyski** (wymagane): Ustawianie maksymalnej liczby klientów, których można użyć w tym samym czasie wersji testowej. Każdy jednoczesnych użytkowników będą wymagały licencją Dynamics 365 podczas wersji testowej jest aktywny, więc należy upewnić się, że masz wystarczającą liczbę licencji, które są dostępne do obsługi zestawu maksymalny limit. Zalecanej wartości wynoszącej 3 – 5.

- **Czas trwania wersji testowej** (wymagane): Określa długość czasu, przez jaki wersji testowej pozostaną aktywne przez zdefiniowanie liczby godzin. Po tym wiele godzin sesja kończy i nie będzie używać jednej z Twoich licencji. Zaleca się wartość 2 do 24 godzin w zależności od złożoności ofertę. Ten czas trwania może tylko pasujących zestaw przez całkowitą liczbę godzin (np. godziny "2", "1.5" jest nieprawidłowy).  Użytkownik może zażądać nową sesję, gdyby zabrakło czasu i chcesz ponownie uzyskać dostęp w wersji testowej.

- **Adres URL wystąpienia** (wymagane): Adres URL, gdzie klient rozpocznie ich wersji testowej. Zazwyczaj adres URL wystąpienia usługi Dynamics 365, z tą aplikacją z przykładowymi danymi zainstalowany (np. https://testdrive.crm.dynamics.com).

- **Adres URL interfejsu API sieci Web wystąpienia** (wymagane): Pobieranie adresu URL interfejsu API sieci Web dla wystąpienia usługi Dynamics 365, logując się do konta usługi Microsoft 365 i przechodząc do **ustawienia** \&gt; **Dostosowywania** \&gt; **Zasoby dla deweloperów** \&gt; **Wystąpienia interfejsu API sieci Web (adres URL katalogu głównego usługi)** , skopiuj adres URL tutaj (np. https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Nazwa roli** (wymagane): Podaj nazwę roli zabezpieczeń, zdefiniowanych w niestandardowych wersji testowej Dynamics 365. Będzie można przypisać do użytkownika podczas ich wersji testowej (np. test-drive oparta na roli).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Pomoc konfiguracji dla wersji testowej aplikacji logiki

Wszystkie produkty niestandardowego należy używać tego typu testu Szablon wdrożenia dysku, który obejmuje szereg różnych architektur złożonych rozwiązań. Aby uzyskać więcej informacji o konfigurowaniu aplikacji logiki wersje testowe, odwiedź stronę [operacji](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [zaangażowania](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w witrynie GitHub.

- **Region** (wymagane, pojedynczego wyboru Lista rozwijana lista): Obecnie istnieją 26 regionach obsługiwanej przez platformę Azure, której wersji testowej mogą być udostępniane. Zasoby dla aplikacji logiki zostanie wdrożony w regionie, którą wybierzesz. Jeśli Twoja aplikacja logiki ma żadnych niestandardowych zasobów przechowywanych w określonym regionie, upewnij się, że ten region wybrany w tym miejscu. Najlepszym sposobem, w tym celu jest w pełni wdrażanie aplikacji logiki lokalnie w Twojej subskrypcji platformy Azure w portalu i sprawdź, czy działa ona poprawnie przed wykonaniem tego wyboru.

- **Maksymalna liczba współbieżnych testu dyski** (wymagane): Ustawianie maksymalnej liczby klientów, których można użyć w tym samym czasie wersji testowej. Tych testów, które dyski są już wdrożone, dzięki czemu klienci mogą szybko uzyskiwać do nich dostęp bez oczekiwania na wdrożenie.

- **Czas trwania wersji testowej** (wymagane): Określa długość czasu, przez jaki wersji testowej pozostaną aktywne w liczba godzin. Wersji testowej kończy się automatycznie po zakończeniu tego okresu.

- **Nazwa grupy zasobów platformy Azure** (wymagane): Wprowadź [grupy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nazwę, której wersji testowej aplikacji logiki jest zapisywany.

- **Nazwa aplikacji logiki platformy Azure** (wymagane): Wprowadź nazwę aplikacji logiki, która przypisuje wersję testową dla użytkownika. Ta aplikacja logiki musi zostać zapisany w grupie zasobów platformy Azure powyżej.

- **Nazwa aplikacji logiki anulowania aprowizacji** (wymagane): Wprowadź nazwę aplikacji logiki, która deprovisions wersji testowej, po zakończeniu pracy klienta. Ta aplikacja logiki musi zostać zapisany w grupie zasobów platformy Azure powyżej.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Pomoc konfiguracji nie jest wymagana dla usługi Power BI wersje testowe

Produkty, które pokazują, interaktywne wizualizacje usługi Power BI można użyć łącza osadzonego na udostępnianie niestandardowej pulpit nawigacyjny jako ich wersji testowej, żadne dodatkowe techniczne wymagana jest konfiguracja. Dowiedz się więcej o konfigurowaniu[usługi Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) szablonu aplikacji.

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

W celu wdrożenia wersji testowej w Twoim imieniu, Utwórz i podaj osobnych, unikatowych subskrypcji platformy Azure. (Nie wymagane dla usługi Power BI wersje testowe).

- **Identyfikator subskrypcji platformy Azure** (wymagane dla usługi Azure Resource Manager oraz Logic apps): Wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure za użycie zasobów, raportowanie i rozliczeń. Firma Microsoft zaleca, które należy wziąć pod uwagę [tworzenia oddzielną subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) na potrzeby wersje testowe, jeśli nie masz jeszcze takiego. Możesz znaleźć swój identyfikator subskrypcji platformy Azure, logując się do [witryny Azure portal](https://portal.azure.com/) i przechodząc do **subskrypcje** kartę menu po lewej stronie. Należy wybrać kartę wyświetli Identyfikatora subskrypcji (np. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Identyfikator dzierżawy usługi Azure AD** (wymagane): Wprowadź usługi Azure Active Directory (AD) [identyfikator dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id). Aby znaleźć ten identyfikator, zaloguj się do [witryny Azure portal](https://portal.azure.com/), wybierz kartę usługi Active Directory w menu po lewej stronie, wybierz **właściwości** , poszukaj **identyfikator katalogu** numer na liście (np. 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy Twojej organizacji za pomocą adresu URL nazwę domeny w: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

- **Nazwa dzierżawy usługi Azure AD** (wymagane dla usłudze Dynamics 365): Wprowadź nazwę usługi Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [witryny Azure portal](https://portal.azure.com/), w prawym górnym rogu, nazwa dzierżawy będą wyświetlane w swojej nazwy konta.

- **Identyfikator aplikacji w usłudze Azure AD** (wymagane): Wprowadź usługi Azure Active Directory (AD) [identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Aby znaleźć ten identyfikator, zaloguj się do [witryny Azure portal](https://portal.azure.com/), wybierz kartę usługi Active Directory w menu po lewej stronie, wybierz **rejestracje aplikacji**, poszukaj **identyfikator aplikacji** numer na liście (np. 50c464d3-4930-494c-963c-1e951d15360e).

- **Klucz aplikacji w usłudze Azure AD** (wymagane): Wprowadź usługi Azure Active Directory (AD) [klucz aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Aby znaleźć ten identyfikator, zaloguj się do [witryny Azure portal](https://portal.azure.com/), wybierz kartę usługi Active Directory w menu po lewej stronie, wybierz **rejestracje aplikacji** , a następnie wybierz **ustawienia**  >  **Klucze**.

Pamiętaj, aby **Zapisz** przed przejściem do następnej sekcji!

### <a name="test-drive-listings-optional"></a>Testowanie ofert dysku (opcjonalnie)

**Ofert wersji testowej** znajdującym się opcja **przetestuj** karta zawiera języki (i rynków), których wersja próbna jest dostępna, obecnie angielski (Stany Zjednoczone) jest jedyną lokalizacją dostępne . Ponadto ta strona wyświetla stan listy specyficzny dla języka i daty/godziny, który został dodany. Należy zdefiniować szczegóły (opis, Podręcznik użytkownika, filmy itp.) dysku testu dla każdego języka/rynku.

- **Opis** (wymagane): Opisz wersji testowej, co zostanie potwierdzona, cele użytkownikowi eksperymentować, funkcje, aby zapoznać się z oraz wszelkie istotne informacje, aby pomóc użytkownikowi określanie, czy można uzyskać oferty. W tym polu można wprowadzić maksymalnie 3000 znaków tekstu. 

- **Dostęp do informacji o** (wymagane dla usługi Azure Resource Manager i logiki wersje testowe): Wyjaśniono, klient musi wiedzieć, aby uzyskać dostęp do tej wersji testowej. Opisano scenariusz przy użyciu oferty i dokładnie co klient powinien wiedzieć dostęp do funkcji w wersji testowej. W tym polu można wprowadzić maksymalnie 10 000 znaków tekstu.

- **Podręcznik użytkownika** (wymagane): Szczegółowy przewodnik środowiska testowego dysku. Podręcznik użytkownika powinno obejmować dokładnie co chcesz klientowi uzyskać występowaniu wersji testowej i służyć jako odwołanie w razie jakichkolwiek pytań, które mogą mieć. Plik musi być w formacie PDF i będzie nosić nazwę (maks. 255 znaków) po przekazaniu.

- **Wideo: Dodawanie filmów wideo** (opcjonalnie): Filmy wideo mogą być przekazywane do usługi YouTube lub Vimeo i odwołujesz przy użyciu łącza i miniaturę obrazu (w pikselach 533 x 324), dzięki czemu odbiorcy mogą wyświetlać przewodnik informacji ułatwiających im lepiej zrozumieć wersji testowej, w tym sposób pomyślnie korzystać z funkcji usługi oferty i zrozumieć scenariusze, które wyróżniają korzyści.
  - **Nazwa** (wymagane)
  - **Adres URL (w usłudze YouTube lub Vimeo tylko)** (wymagane)
  - **Miniatura (533 x 324px)** : Plik obrazu musi być w formacie PNG.

Wybierz **Zapisz** po wykonaniu tych pól.

## <a name="publish"></a>Publikowanie

#### <a name="submit-offer-to-preview"></a>Prześlij oferują one w wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz **publikowania** w prawym górnym rogu portalu. Będzie skierowana do **przeglądu i publikowanie** strony. 

Jeśli po raz pierwszy publikowania tej oferty, możesz to zrobić:

- Wyświetlany jest stan ukończenia dla każdej sekcji oferty.
    - *Nierozpoczęte* — oznacza, że sekcja została nienaruszone i musi zostać ukończona.
    - *Niekompletne* — oznacza, że sekcja zawiera błędy, które trzeba naprawić lub wymaga więcej informacji, należy podać. Wróć do pliku, a następnie go zaktualizować.
    - *Pełne* — oznacza, że sekcja jest pełna, podano wszystkich wymaganych danych i nie ma żadnych błędów. Wszystkie sekcje oferty musi być w stanie ukończone przed przesłaniem tej oferty.
- Zawierają instrukcje testowania do zespołu certyfikacji, aby upewnić się, że aplikacja zostanie przetestowana poprawnie, oprócz wszelkie dodatkowe uwagi, pomaga w zrozumieniu Twojej aplikacji.
- Przesyłać oferty do opublikowania, wybierając **przesyłania**. Wyślemy wiadomość e-mail z informacją, gdy wersja zapoznawcza oferty jest dostępne dla Ciebie przejrzeć i zatwierdzić. Musisz powrócić do Centrum partnerskiego i wybierz **gotowej do pracy** oferty do opublikowania oferty publicznie (lub jeśli prywatnej oferują odbiorcom prywatnych).

## <a name="next-steps"></a>Kolejne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace komercyjnych](./update-existing-offer.md)
