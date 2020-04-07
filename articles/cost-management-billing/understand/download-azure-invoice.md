---
title: Wyświetlanie i pobieranie faktury platformy Azure
description: Opis sposobu wyświetlania i pobierania faktury za korzystanie z platformy Azure.
keywords: faktura rozliczeniowa, pobieranie faktury, faktura platformy azure, dane użycia platformy azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478996"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Wyświetlanie i pobieranie faktury platformy Microsoft Azure

Fakturę możesz pobrać w [witrynie Azure Portal](https://portal.azure.com/) lub zażądać jej wysłania w wiadomości e-mail. Jeśli jesteś klientem platformy Azure z umową Enterprise Agreement (umową EA), nie możesz pobrać faktur swojej organizacji. Zamiast tego faktury są wysyłane do osoby skonfigurowanej jako odbiorca faktur dla rejestracji.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>Czas generowania faktur

Faktura jest generowana na podstawie typu konta rozliczeniowego. Faktury są tworzone dla kont rozliczeniowych programu Microsoft Online Service Program (MOSP), Umowy z Klientem Microsoft (MCA) i umowy Microsoft Partner Agreement (MPA). Faktury są także generowane dla kont rozliczeniowych umowy Enterprise Agreement (EA). Jednak faktury dla kont z rozliczeniowych umowy EA nie są wyświetlane w witrynie Azure Portal.

Aby dowiedzieć się więcej na temat kont rozliczeniowych i identyfikowania typu konta rozliczeniowego, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](../manage/view-all-accounts.md).

## <a name="invoices-for-mosp-billing-accounts"></a>Zarządzanie kontami rozliczeniowymi programu MOSP

Konto rozliczeniowe programu MOSP jest tworzone podczas rejestrowania się na platformie Azure za pomocą witryny internetowej platformy Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu utworzenia [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe dla umowy MCA.

Jeśli nie masz pewności co do typu Twojego konta rozliczeniowego, zobacz [Sprawdzanie typu konta rozliczeniowego](../manage/view-all-accounts.md#check-the-type-of-your-account) przed wykonaniem instrukcji opisanych w tym artykule. 

Konto rozliczeniowe programu MOSP może zawierać następujące faktury:

**Opłaty za usługi Azure** — faktura jest generowana dla każdej subskrypcji platformy Azure, która zawiera zasoby platformy Azure używane przez subskrypcję. Faktura zawiera opłaty za okres rozliczeniowy. Okres rozliczeniowy jest określony przez dzień miesiąca utworzenia subskrypcji.

Na przykład Jan tworzy subskrypcję *Azure-01* 5 marca i *Azure-02* 10 marca. Na fakturze za subskrypcję *Azure-01* będą znajdować się opłaty naliczone za okres od piątego dnia miesiąca do czwartego dnia następnego miesiąca. Na fakturze za subskrypcję *Azure-02* będą znajdować się opłaty naliczone za okres od dziesiątego dnia miesiąca do dziewiątego dnia następnego miesiąca. Faktury dla wszystkich subskrypcji platformy Azure są zwykle generowane w dniu miesiąca utworzenia konta, lecz może to być także do dwóch dni później. W tym przykładzie, jeśli Jan utworzył konto 2 lutego, faktury dla obu subskrypcji, *Azure-01* i *Azure-02*, będą standardowo generowane w drugim dniu każdego miesiąca, przy czym możliwe jest, że do dwóch dni później.

**Portal Azure Marketplace, rezerwacje i maszyny wirtualne typu spot** — faktura jest generowana dla rezerwacji, produktów z platformy handlowej i maszyn wirtualnych typu spot kupionych za pomocą subskrypcji. Faktura zawiera odpowiednie opłaty z poprzedniego miesiąca. Na przykład Jan kupił rezerwację 1 marca i kolejną rezerwacją 30 marca. Pojedyncza faktura zostanie wygenerowana dla obu rezerwacji w kwietniu. Faktury za portal Azure Marketplace, rezerwacje i maszyny wirtualne typu spot są zawsze generowana około dziewiątego dnia miesiąca.

Jeśli płacisz za platformę Azure za pomocą karty kredytowej i kupujesz rezerwację, platforma Azure wygeneruje fakturę natychmiast. Jednak w przypadku rozliczania za pomocą faktury opłata za rezerwację zostanie naliczona w ramach następnej faktury miesięcznej.

**Plan pomocy technicznej platformy Azure** — faktura jest generowana co miesiąc dla subskrypcji planu pomocy technicznej. Pierwsza faktura jest generowana w dniu zakupu lub do dwóch dni później. Kolejne faktury za plan pomocy technicznej są zwykle generowane w dniu miesiąca utworzenia konta, lecz może to być także do dwóch dni później.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Pobieranie faktury za subskrypcję platformy Azure dla programu MOSP

Faktura jest generowana tylko dla subskrypcji należącej do konta rozliczeniowego programu MOSP. [Sprawdzanie dostępu do konta programu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Do pobrania faktury jest wymagana rola administratora konta dla subskrypcji. Użytkownik z rolami właściciela, współautora lub czytelnika może pobrać fakturę, jeśli administrator konta udzielił mu uprawnienia. Aby uzyskać więcej informacji, zobacz [Zezwalanie użytkownikom na pobieranie faktur](../manage/manage-billing-access.md#opt-in).

1. Wybierz subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure Portal.
1. Wybierz pozycję **Faktury** w sekcji rozliczeń.  
    ![Zrzut ekranu przedstawiający użytkownika wybierającego opcję faktur dla subskrypcji](./media/download-azure-invoice/select-subscription-invoice.png)
1. Wybierz pozycję **Pobierz**, aby pobrać fakturę jako plik PDF, a następnie wybierz pozycję **Pobierz** w sekcji faktury.  
    [![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania i łączne opłaty za każdy okres rozliczeniowy](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. Możesz również pobrać dzienne zestawienie ilości zużytych zasobów i opłat, wybierając pozycję **Pobierz** w sekcji szczegółów użycia. Przygotowanie pliku CSV może potrwać kilka minut.  
    ![Zrzut ekranu przedstawiający stronę Pobieranie faktur i danych użycia](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Aby uzyskać więcej informacji na temat faktury, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](../understand/review-individual-bill.md). Aby uzyskać pomoc dotyczącą zarządzania kosztami, zobacz [Zapobieganie powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzanie kosztami](../manage/getting-started.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Pobieranie faktury dla planu pomocy technicznej programu MOSP

Faktura jest generowana tylko dla subskrypcji planu pomocy technicznej należącej do konta rozliczeniowego programu MOSP. [Sprawdzanie dostępu do konta programu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Do pobrania faktury jest wymagana rola administratora konta dla subskrypcji planu pomocy technicznej.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/download-azure-invoice/search-cmb.png)
1.  Wybierz pozycję **Faktury** po lewej stronie.
1.  Wybierz subskrypcję planu pomocy technicznej, a następnie wybierz pozycję **Pobierz**.  
    [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  Wybierz pozycję **Pobierz**, aby pobrać fakturę w postaci pliku PDF.  
    ![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania i łączne opłaty za każdy okres rozliczeniowy](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>Zezwalanie innym osobom na pobieranie faktury dla subskrypcji programu MOSP

Plik PDF faktury zawiera informacje osobiste dotyczące administratora konta, dlatego administrator konta subskrypcji musi udzielić innym użytkownikom uprawnienia do pobierania faktury. Po udzieleniu uprawnienia następujące osoby i użytkownicy z odpowiednimi rolami mogą pobierać faktury dla subskrypcji:

- Użytkownicy
- Grupy
- Jednostki usługi z właścicielem
- Współautor
- Czytelnik
- Administrator dostępu użytkownika
- Czytelnik rozliczeń
- Współadministrator
- Administrator usługi

Aby pobrać fakturę:

1.  Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta dla subskrypcji.
1.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1.  Wybierz pozycję **Faktury** po lewej stronie.
1.  Wybierz subskrypcję platformy Azure, a następnie wybierz pozycję **Dostęp do faktury**.  
    [![Zrzut ekranu przedstawiający wybieranie dostępu do faktury](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  Wybierz pozycję **Włączone**, a następnie **Zapisz** w górnej części strony.  
    ![Zrzut ekranu przedstawiający włączanie dostępu do faktury](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>Uzyskiwanie faktury subskrypcji programu MOSP za pomocą wiadomości e-mail

Musisz mieć rolę administratora konta dla subskrypcji lub planu pomocy technicznej, aby zgodzić się na otrzymywania faktur pocztą e-mail. Faktury e-mail są dostępne tylko dla subskrypcji i planów pomocy technicznej, a nie są dostępne dla rezerwacji ani zakupów w portalu Azure Marketplace. Po udzieleniu zgody możesz dodać kolejnych adresatów, którzy także będą otrzymywać fakturę pocztą e-mail.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1.  Wybierz pozycję **Faktury** po lewej stronie.
1.  Wybierz subskrypcję platformy Azure lub subskrypcję planu pomocy technicznej, a następnie wybierz pozycję **Wyślij fakturę pocztą e-mail**.  
    [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. Wybierz pozycję **Wyraź zgodę** i zaakceptuj warunki.  
    ![Zrzut ekranu pokazujący 2. krok przepływu z wyrażeniem zgody](./media/download-azure-invoice/invoice-article-step02.png)
1. Faktura jest wysyłana na preferowany adres e-mail komunikacji. Adres e-mail można zaktualizować w [informacjach kontaktowych konta rozliczeniowego](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties).  
    ![Zrzut ekranu pokazujący 3. krok przepływu z wyrażeniem zgody](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>Udostępnianie faktur dla programu MOSP innym osobom za pośrednictwem wiadomości e-mail

Comiesięczne faktury dla subskrypcji i planu pomocy technicznej możesz udostępniać zespołowi księgowemu lub wysłać je na swój inny adres e-mail.

1. Wykonaj kroki opisane w sekcji [Pobieranie faktur dla subskrypcji i planu pomocy technicznej za pomocą wiadomości e-mail](#get-mosp-subscription-invoice-in-email) i wybierz pozycję **Konfiguruj adresatów**.  
    ![Zrzut ekranu przedstawiający użytkownika wybierającego pozycję Konfiguruj adresatów](./media/download-azure-invoice/invoice-article-step03.png)
1. Podaj adres e-mail, a następnie wybierz pozycję **Dodaj adresatów**. Możesz dodać wiele adresów e-mail.  
    ![Zrzut ekranu przedstawiający użytkownika dodającego kolejnych adresatów](./media/download-azure-invoice/invoice-article-step04.png)
1. Po dodaniu wszystkich adresów e-mail wybierz pozycję **Gotowe** w dolnej części ekranu.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Faktury dla kont rozliczeniowych umów MCA i MPA

Konto rozliczeniowe umowy MCA jest tworzone w ramach współpracy organizacji z przedstawicielem firmy Microsoft w celu podpisania umowy MCA. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu utworzenia [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe dla umowy MCA. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do konta rozliczeniowego umowy MCA](../understand/mca-overview.md).

Konto rozliczeniowe dla umowy MPA jest tworzone dla dostawców rozwiązań w chmurze (CSP) w celu zarządzania ich klientami w nowym środowisku handlowym. Partnerzy muszą mieć co najmniej jednego klienta z [planem platformy Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan), aby mogli zarządzać kontem rozliczeniowym w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do konta rozliczeniowego umowy MPA](../understand/mpa-overview.md).

Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera odpowiednie opłaty za wszystkie subskrypcje platformy Azure i inne zakupy w poprzednim miesiącu. Na przykład Jan utworzył subskrypcję *Azure-01* 5 marca i *Azure-02* 10 marca. Subskrypcję *Azure-pomoc-01* kupił 28 marca przy użyciu profilu *Profil rozliczeniowy 01*. Jan otrzyma jedną fakturę na początku kwietnia, która będzie zawierać opłaty za subskrypcje platformy Azure i planu pomocy technicznej.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Pobieranie faktury dla profilu rozliczeniowego programu MCA lub MPA

Aby pobierać faktury z witryny Azure Portal, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego. Użytkownicy z rolą właściciela, współautora lub czytelnika dla konta rozliczeniowego mogą pobierać faktury dla wszystkich profilów rozliczeniowych w ramach konta.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1. Wybierz pozycję **Faktury** po lewej stronie.
    [![Zrzut ekranu przedstawiający stronę faktur dla konta rozliczeniowego umowy MCA](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. W tabeli faktur wybierz fakturę, którą chcesz pobrać.
1. Wybierz pozycję **Pobierz fakturę jako plik pdf** w górnej części strony.  
    ![Zrzut ekranu przedstawiający pobieranie faktury w postaci pliku pdf](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. Możesz także pobrać plik CSV z dziennym zestawieniem ilości zużytych zasobów i szacowanymi opłatami, wybierając pozycję **Pobierz użycie platformy Azure**. Przygotowanie pliku CSV może potrwać kilka minut.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Pobieranie faktury dla profilu rozliczeniowego za pomocą wiadomości e-mail

Aby zaktualizować preferencje wiadomości e-mail dla faktury, musisz mieć rolę właściciela lub współautora dla profilu rozliczeniowego lub jego konta rozliczeniowego. Po udzieleniu zgody wszyscy użytkownicy z rolą właściciela, współautora, czytelnika lub menedżera faktur dla profilu rozliczeniowego będą otrzymywać fakturę w wiadomości e-mail. 

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1.  Wybierz pozycję **Faktury** po lewej stronie, a następnie wybierz pozycję **Wyślij fakturę pocztą e-mail** w górnej części strony.  
    [![Zrzut ekranu przedstawiający stronę faktur dla konta rozliczeniowego umowy MCA](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Jeśli masz wiele profilów rozliczeniowych, wybierz profil rozliczeniowy, a następnie pozycję **Wyraź zgodę**.  
    ![Zrzut ekranu przedstawiający stronę faktur dla konta rozliczeniowego umowy MCA](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  Wybierz pozycję **Update** (Aktualizuj).

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>Nadawanie innym osobom dostępu do faktur dla umowy MCA lub MPA

Możesz nadać innym osobom dostęp do wyświetlania, pobierania i opłacania faktur, przypisując im rolę menedżera faktur dla profilu rozliczeniowego umowy MCA lub MPA. Jeśli wybrano opcję otrzymywania faktury w wiadomości e-mail, użytkownicy ci będą także otrzymywać faktury w wiadomości e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1. Po lewej stronie wybierz pozycję **Profile rozliczeniowe**. Z listy profilów rozliczeniowych wybierz profil rozliczeniowy, dla którego chcesz przypisać rolę menedżera faktur.  
   ![Zrzut ekranu przedstawiający listę profilów rozliczeniowych](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Wybierz pozycję **Kontrola dostępu (IAM)** po lewej stronie, a następnie wybierz pozycję **Dodaj** w górnej części strony.  
    ![Zrzut ekranu przedstawiający stronę kontroli dostępu](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. Z listy rozwijanej Rola wybierz pozycję **Menedżer faktur**. Wprowadź adres e-mail użytkownika, któremu chcesz nadać dostęp. Wybierz przycisk **Zapisz**, aby przypisać rolę.  
   ![Zrzut ekranu pokazujący dodawanie użytkownika jako menedżera faktur](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a> Dlaczego możesz nie widzieć faktury

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni. 
- Platforma Azure tworzy rozliczenia kilka dni po upływie okresu rozliczeniowego. W związku z tym faktura mogła jeszcze nie zostać wygenerowana.
- Nie masz uprawnień do wyświetlania faktur.
    W przypadku konta rozliczeniowego umowy MCA lub MPA, aby wyświetlić faktury profilu rozliczeniowego, musisz mieć rolę właściciela, współautora, czytelnika lub menedżera faktur dla profilu rozliczeniowego albo właściciela, współautora lub czytelnika dla konta rozliczeniowego. W przypadku innych subskrypcji faktury mogą nie być widoczne, jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](../manage/manage-billing-access.md).
- W przypadku konta rozliczeniowego programu MOSP i zarejestrowania się w celu uzyskania bezpłatnego konta platformy Azure lub subskrypcji z miesięczną kwotą środków, otrzymasz fakturę tylko wtedy, gdy miesięczna kwota środków zostanie przekroczona. W przypadku konta rozliczeniowego dla umowy MCA lub MPA zawsze otrzymujesz fakturę.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktur i opłat, zobacz:

- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](download-azure-daily-usage.md)
- [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](review-individual-bill.md)
- [Omówienie terminów na fakturze za korzystanie z platformy Azure](understand-invoice.md)

Jeśli masz umowę MCA, zobacz:

- [Omówienie opłat na fakturze dla profilu rozliczeniowego](review-customer-agreement-bill.md)
- [Omówienie terminów na fakturze dla profilu rozliczeniowego](mca-understand-your-invoice.md)
- [Omówienie pliku użycia i opłat dotyczących platformy Azure dla profilu rozliczeniowego](mca-understand-your-usage.md)


