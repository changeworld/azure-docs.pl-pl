---
title: Plik dyrektywy include
description: Plik dyrektywy include
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321707"
---
Karta **Dysk testowy** umożliwia skonfigurowanie demonstracji (lub "jazdy testowej"), która umożliwi klientom wypróbowanie oferty przed jej zakupem. Dowiedz się więcej w artykule [Co to jest jazda testna?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Jeśli nie chcesz już dostarczać dysku testowego dla swojej oferty, wróć do strony **Ustawienia oferty** i odeślij pole wyboru **Włącz jazdę próbną**.

### <a name="technical-configuration"></a>Konfiguracja techniczna
Dostępne są następujące typy dysków testowych, z których każdy ma własne wymagania techniczne konfiguracji.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikacja logika](#technical-configuration-for-logic-app-test-drive)
- [Usługa Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (konfiguracja techniczna nie jest wymagana)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Konfiguracja techniczna dla dysku testowego usługi Azure Resource Manager

Szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, które składają się na rozwiązanie. Produkty, które pasują do tego scenariusza, używają tylko zasobów platformy Azure. Dowiedz się więcej o konfigurowaniu [dysku testowego usługi Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiony** (wymagane): Obecnie istnieje 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zazwyczaj należy udostępnić jazdę próbną w regionach, w których przewiduje się największą liczbę klientów, aby mogli wybrać najbliższy region dla najlepszej wydajności. Należy upewnić się, że subskrypcja może wdrożyć wszystkie zasoby potrzebne w każdym z regionów, które wybierasz.

- **Wystąpienia:** Wybierz typ (gorący lub zimny) i liczbę dostępnych wystąpień, które zostaną pomnożone przez liczbę regionów, w których oferta jest dostępna.

**Hot:** Ten typ wystąpienia jest wdrażany i oczekuje na dostęp dla wybranego regionu. Klienci mogą natychmiast uzyskać dostęp *do gorących* wystąpień dysku testowego, zamiast czekać na wdrożenie. Kompromisem jest to, że te wystąpienia są zawsze uruchomione w ramach subskrypcji platformy Azure, więc poniosą one większy koszt czasu pracy bez przestojów. Zdecydowanie zaleca się mieć co najmniej jedno *wystąpienie Hot,* ponieważ większość klientów nie chce czekać na pełne wdrożenia, co powoduje spadek użycia klienta, jeśli nie jest dostępne żadne *wystąpienie Hot.*

**Zimno:** Ten typ wystąpienia reprezentuje całkowitą liczbę wystąpień, które mogą być ewentualnie wdrożone na region. Zimne wystąpienia wymagają całego szablonu Menedżera zasobów dysku testowego do wdrożenia, gdy klient zażąda dysku testowego, *więc* zimne wystąpienia są znacznie wolniejsze do załadowania niż *wystąpienia hot.* Kompromisem jest to, że trzeba płacić tylko za czas trwania dysku testowego, *nie* zawsze jest uruchomiony w ramach subskrypcji platformy Azure, jak w przypadku *wystąpienia Hot.*

- **Szablon usługi Azure Resource Manager na dysku testowym:** przekaż plik zip zawierający szablon usługi Azure Resource Manager.  Dowiedz się więcej o tworzeniu szablonu usługi Azure Resource Manager w artykule Szybki start [Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas, przez który dysk testowy pozostanie aktywny w ciągu kilku godzin. Dysk testowy kończy się automatycznie po zakończeniu tego okresu. Czas trwania ten może być ustawiony tylko przez liczbę godzin (np. "2" godzin, "1.5" jest nieważne).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Konfiguracja techniczna dla jazdy testowej Dynamics 365

Firma Microsoft może usunąć złożoność konfigurowania dysku testowego przez hosting i utrzymywanie inicjowania obsługi administracyjnej i wdrażania usługi przy użyciu tego typu dysku testowego. Konfiguracja dla tego typu hostowanego dysku testowego jest taka sama, niezależnie od tego, czy dysk testowy jest przeznaczony dla odbiorców Business Central, Customer Engagement lub Operations.

- **Maksymalna liczba równoczesnych dysków testowych** (wymagana): ustaw maksymalną liczbę klientów, którzy mogą korzystać z dysku testowego w tym samym czasie. Każdy równoczesnych użytkowników będzie używać licencji Dynamics 365, gdy dysk testowy jest aktywny, więc należy upewnić się, że masz wystarczająco dużo licencji dostępnych do obsługi maksymalnego limitu ustawionego. Zalecana wartość 3-5.

- **Czas trwania jazdy próbnej** (wymagany): Wprowadź czas, przez który testowa jazda będzie aktywna, definiując liczbę godzin. Po tych wielu godzinach sesja zostanie zakończona i nie będzie już zużywać jednej z licencji. Zalecamy wartość 2-24 godzin w zależności od złożoności oferty. Czas trwania ten może być ustawiony tylko przez liczbę godzin (np. "2" godzin, "1.5" jest nieważne).  Użytkownik może zażądać nowej sesji, jeśli zabraknie im czasu i chcesz ponownie uzyskać dostęp do dysku testowego.

- **Adres URL wystąpienia** (wymagany): adres URL, pod którym klient rozpocznie jazdę testowa. Zazwyczaj adres URL wystąpienia Dynamics 365 z zainstalowaną aplikacją z `https://testdrive.crm.dynamics.com`zainstalowanymi przykładowymi danymi (np. ).

- **Adres URL interfejsu API sieci Web wystąpienia** (wymagane): pobierz adres URL interfejsu API sieci Web dla wystąpienia dynamics 365, logując się do konta usługi Microsoft 365 i przechodząc do **ustawień** \&gt; **Dostosowanie** \&gt; **Zasoby** \&dla deweloperów gt; **Interfejs API sieci Web instancji (adres URL katalogu głównego usługi),** skopiuj adres URL znajdujący się tutaj (np. `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Nazwa roli** (wymagana): Podaj nazwę roli zabezpieczeń zdefiniowaną na niestandardowej dysku testowym Dynamics 365. Zostanie to przypisane do użytkownika podczas jazdy próbne (np. test-drive-role).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Konfiguracja techniczna dla aplikacji logiki jazdy testowej

Wszystkie produkty niestandardowe należy użyć tego typu szablonu wdrażania dysku testowego, który obejmuje wiele złożonych architektur rozwiązań. Aby uzyskać więcej informacji na temat konfigurowania dysków testowych aplikacji logiki, odwiedź [witrynę Operacje](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [customer engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w usłudze GitHub.

- **Region** (wymagana lista rozwijana z jednym wyborem): obecnie istnieje 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zasoby aplikacji logiki zostaną wdrożone w wybranym regionie. Jeśli aplikacja logiki ma żadnych zasobów niestandardowych przechowywanych w określonym regionie, upewnij się, że region jest zaznaczony w tym miejscu. Najlepszym sposobem, aby to zrobić, jest pełne wdrożenie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa poprawnie przed dokonaniem tego wyboru.

- **Maksymalna liczba równoczesnych dysków testowych** (wymagana): ustaw maksymalną liczbę klientów, którzy mogą korzystać z dysku testowego w tym samym czasie. Te dyski testowe są już wdrożone, dzięki czemu klienci mogą natychmiast uzyskać do nich dostęp bez oczekiwania na wdrożenie.

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas, przez który dysk testowy pozostanie aktywny w ciągu kilku godzin. Po zakończeniu tego okresu dysk testowy kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure** (wymagana): wprowadź nazwę grupy zasobów platformy [Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) w której jest zapisywany dysk testowy aplikacji logiki.

- **Nazwa aplikacji logiki platformy Azure** (wymagane): Wprowadź nazwę aplikacji logiki, która przypisuje dysku testowego do użytkownika. Ta aplikacja logiki musi być zapisany w grupie zasobów platformy Azure powyżej.

- **Nazwa aplikacji logiki deprovision** (wymagane): Wprowadź nazwę aplikacji logiki, która usuwa aprowizji dysku testowego po zakończeniu klienta. Ta aplikacja logiki musi być zapisany w grupie zasobów platformy Azure powyżej.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Konfiguracja techniczna nie jest wymagana dla dysków testowych usługi Power BI

Produkty, które chcą zademonstrować interaktywną wizualizację usługi Power BI, mogą używać wbudowanego łącza do udostępniania niestandardowego pulpitu nawigacyjnego jako dysku testowego, bez konieczności dalszej konfiguracji technicznej. Dowiedz się więcej o konfigurowaniu aplikacji szablonów[usługi Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby wdrożyć jazdę testową w Twoim imieniu, utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure. (Nie jest wymagane w przypadku dysków testowych usługi Power BI).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla usługi Azure Resource Manager i aplikacje logiki): wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure w celu raportowania użycia zasobów i rozliczeń. Zaleca się, aby rozważyć [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) do użycia w przypadku jazdy testowych, jeśli nie masz jeszcze. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [witryny Azure portal](https://portal.azure.com/) i przechodząc do karty **Subskrypcje** menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (np.

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): wprowadź identyfikator [dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **polecenie Właściwości** , a następnie poszukaj podanego numeru **identyfikatora katalogu** (np. 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy organizacji przy użyciu [https://www.whatismytenantid.com](https://www.whatismytenantid.com)adresu URL nazwy domeny pod adresem: .

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla usługi Dynamic 365): wprowadź nazwę usługi Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [witryny Azure portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona pod nazwą konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagany): wprowadź identyfikator aplikacji usługi Azure Active Directory (AD). [application ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **pozycję Rejestracje aplikacji**, a następnie poszukaj numeru **identyfikatora aplikacji** na liście (np. 50c464d3-4930-494c-963c-1e951d15360e).

- **Klucz tajny klienta aplikacji usługi Azure AD** (wymagany): wprowadź klucz tajny [klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD . Aby znaleźć tę wartość, zaloguj się do [witryny Azure portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz aplikację dysku testowego. Następnie wybierz **pozycję Certyfikaty i wpisy tajne**, wybierz pozycję Nowy klucz tajny **klienta**, wprowadź opis, wybierz pozycję **Nigdy w** obszarze **Wygasa**, a następnie wybierz pozycję **Dodaj**. Pamiętaj, aby skopiować wartość. (Nie odejdź od strony, zanim to zrobisz, w przeciwnym razie nie będziesz mieć dostępu do wartości).

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji!

### <a name="test-drive-listings-optional"></a>Lista dysków testowych (opcjonalnie)

Opcja **Aukcje na dysku testowym znajdująca** się na karcie **Dysk testowy** wyświetla języki (i rynki), w których jest dostępna jazda próbna, obecnie dostępna jest w języku angielskim (Stany Zjednoczone). Ponadto na tej stronie jest wyświetlany stan listy specyficznej dla języka oraz data/godzina dodania. Musisz zdefiniować szczegóły jazdy próbnej (opis, instrukcja obsługi, filmy itp.) dla każdego języka / rynku.

- **Opis** (wymagane): Opisz jazdę próbną, co zostanie zademonstrowane, cele, z jakim użytkownik może eksperymentować, funkcje do zbadania i wszelkie istotne informacje, które pomogą użytkownikowi ustalić, czy chcesz uzyskać ofertę. W tym polu można wprowadzić maksymalnie 3000 znaków tekstu. 

- **Informacje o dostępie** (wymagane dla usług Azure Resource Manager i logic dysków testowych): Wyjaśnij, co klient musi wiedzieć, aby uzyskać dostęp do tej dysku testowego i korzystać z niego. Zapoznaj się ze scenariuszem korzystania z oferty i dokładnie tym, co klient powinien wiedzieć, aby uzyskać dostęp do funkcji podczas jazdy próbnego. W tym polu można wprowadzić maksymalnie 10 000 znaków tekstu.

- **Instrukcja obsługi** (wymagana): szczegółowy przewodnik po doświadczeniu jazdy próbnej. Instrukcja obsługi powinna obejmować dokładnie to, co chcesz, aby klient uzyskał z powodu wystąpienia jazdy próbnej i służyć jako punkt odniesienia dla wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i być nazwany (maks. 255 znaków) po przesłaniu.

- **Filmy: dodaj filmy** (opcjonalnie): filmy można przesyłać do YouTube lub Vimeo i odwoływać się tutaj za pomocą linku i obrazu miniatur (533 x 324 piksele), aby klient mógł przeglądać informacje, aby pomóc mu lepiej zrozumieć jazdę próbną, w tym jak skutecznie korzystać z funkcji oferty i zrozumieć scenariusze, które podkreślają ich zalety.
  - **Nazwa** (wymagana)
  - **Adres URL (tylko YouTube lub Vimeo)** (wymagany)
  - **Miniatura (533 x 324px):** Plik obrazu musi być w formacie PNG.

Wybierz **pozycję Zapisz** po wypełnieniu tych pól.
