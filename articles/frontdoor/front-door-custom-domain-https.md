---
title: Samouczek — Konfigurowanie protokołu HTTPS w domenie niestandardowej dla usługi Azure Front Door Service | Microsoft Docs
description: W tym samouczku dowiesz się, jak włączać i wyłączać protokół HTTPS w konfiguracji usługi Azure Front Door Service dla domeny niestandardowej.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: sharadag
ms.openlocfilehash: b99132cceb8981a93a8f1c10ccc488d5806f7254
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050981"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Samouczek: konfigurowanie protokołu HTTPS w niestandardowej domenie usługi Front Door

W tym samouczku przedstawiono, jak włączyć protokół HTTPS dla niestandardowej domeny skojarzonej z usługą Front Door w sekcji hostów frontonu. Dzięki użyciu protokołu HTTPS w domenie niestandardowej (na przykład https:\//www.contoso.com) zyskujesz pewność, że poufne dane są bezpiecznie dostarczane za pośrednictwem szyfrowania TLS/SSL, gdy są wysyłane przez Internet. Gdy przeglądarka internetowa łączy się z witryną internetową za pośrednictwem protokołu HTTPS, weryfikuje, czy certyfikat zabezpieczeń witryny internetowej jest poprawny i czy został wystawiony przez autentyczny urząd certyfikacji. Ten proces zapewnia bezpieczeństwo i chroni aplikacje internetowe przed atakami.

Domyślnie usługa Azure Front Door Service obsługuje protokół HTTPS dla domyślnej nazwy hosta usługi Front Door. Jeśli na przykład utworzysz usługę Front Door (taką jak https:\//contoso.azurefd.net), protokół HTTPS zostanie automatycznie włączony dla żądań kierowanych na adres https://contoso.azurefd.net. Jednak gdy dodasz domenę niestandardową „www.contoso.com”, musisz dodatkowo włączyć protokół HTTPS dla tego hosta frontonu.   

Niektóre z kluczowych atrybutów niestandardowej funkcji HTTPS to:

- Brak dodatkowych kosztów: nie ma kosztów nabycia ani odnowienia certyfikatu ani dodatkowego kosztu związanego z ruchem protokołu HTTPS. 

- Proste włączanie: w witrynie [Azure Portal](https://portal.azure.com) jest dostępna aprowizacja przy użyciu jednego kliknięcia. Aby włączyć tę funkcję, można użyć interfejsu API REST lub innych narzędzi dla deweloperów.

- Dostępne jest kompletne zarządzanie certyfikatami: wszystkie operacje nabywania certyfikatów i zarządzania nimi są obsługiwane za użytkownika. Certyfikaty są automatycznie aprowizowane i odnawiane przed wygaśnięciem ważności, co powoduje wyeliminowanie ryzyka przerwania działania usługi z powodu wygaśnięcia ważności certyfikatu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Włączanie protokołu HTTPS w domenie niestandardowej
> - Używanie certyfikatu zarządzanego przez usługę AFD 
> - Używanie własnego certyfikatu (niestandardowego certyfikatu SSL)
> - Weryfikowanie domeny
> - Wyłączanie protokołu HTTPS w domenie niestandardowej


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door z co najmniej jedną dołączoną domeną niestandardową. Aby uzyskać więcej informacji, zobacz [Samouczek: Dodawanie do usługi Front Door domeny niestandardowej](front-door-custom-domain.md).

## <a name="ssl-certificates"></a>Certyfikaty SSL

Aby włączyć protokół HTTPS w celu bezpiecznego dostarczania zawartości w niestandardowej domenie usługi Front Door, należy użyć certyfikatu SSL. Możesz użyć certyfikatu zarządzanego przez usługę Azure Front Door lub własnego certyfikatu.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Opcja 1 (domyślna): użycie certyfikatu zarządzanego przez usługę Front Door

Korzystając z certyfikatu zarządzanego przez usługę Azure Front Door Service, można włączyć funkcję protokołu HTTPS za pomocą kilku kliknięć. Usługa Azure Front Door w pełni obsługuje zadania zarządzania certyfikatami, takie jak nabywanie i odnawianie. Po włączeniu funkcji proces rozpocznie się automatycznie. Jeśli domena niestandardowa jest już zamapowana do domyślnego hosta frontonu usługi Front Door (`{hostname}.azurefd.net`), nie są wymagane żadne dalsze działania. Usługa Front Door przetworzy kroki i wykona żądanie automatycznie. Jeśli jednak domena niestandardowa jest zmapowana w innej lokalizacji, należy użyć widomości e-mail, aby zweryfikować własność domeny.

Aby włączyć protokół HTTPS w domenie niestandardowej, wykonaj następujące kroki:

1. W [witrynie Azure Portal](https://portal.azure.com) przejdź do profilu **usługi Front Door**.

2. Na liście hostów frontonu wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

3. W sekcji **Protokół HTTPS domen niestandardowych** kliknij pozycję **Włączony** i wybierz pozycję **Zarządzany przez usługę Front Door** jako źródło certyfikatu.

4. Kliknij pozycję Zapisz.

5. Przejdź do kroku [Weryfikowanie domeny](#validate-the-domain).


### <a name="option-2-use-your-own-certificate"></a>Opcja 2: Używanie własnego certyfikatu

Możesz włączyć funkcję HTTPS przy użyciu własnego certyfikatu. Ten proces odbywa się dzięki integracji z usługą Azure Key Vault, umożliwiającą bezpieczne przechowywanie certyfikatów. Ten bezpieczny mechanizm, używany w usłudze Azure Front Door do pobrania certyfikatu, wymaga wykonania kilku dodatkowych czynności. Tworząc certyfikat SSL, musisz korzystać z dozwolonego urzędu certyfikacji (CA). W przeciwnym razie, jeśli skorzystasz z niedozwolonego urzędu certyfikacji, żądanie zostanie odrzucone. Aby uzyskać listę dozwolonych urzędów certyfikacji, zobacz [Dozwolone urzędy certyfikacji umożliwiające korzystanie z niestandardowego protokołu HTTPS w usłudze Azure Front Door Service](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Przygotowywanie certyfikatu i konta usługi Azure Key Vault
 
1. Usługa Azure Key Vault: musisz mieć konto usługi Azure Key Vault uruchomione w ramach tej samej subskrypcji co usługa Front Door, dla której chcesz włączyć niestandardowy protokół HTTPS. Utwórz konto usługi Azure Key Vault, jeśli go nie masz.
 
2. Certyfikaty usługi Azure Key Vault: jeśli masz już certyfikat, możesz przekazać go bezpośrednio na konto usługi Azure Key Vault lub utworzyć nowy certyfikat bezpośrednio za pomocą usługi Azure Key Vault za pośrednictwem jednego z partnerskich urzędów certyfikacji, z którymi zintegrowana jest usługa Azure Key Vault.

> [!WARNING]
> </br> — Usługa Azure Front Door Service obecnie obsługuje tylko konta usługi Key Vault w tej samej subskrypcji co konfiguracja usługi Front Door. Wybranie usługi Key Vault z innej subskrypcji niż usługa Front Door spowoduje błąd.
> </br> — Usługa Azure Front Door Service obecnie obsługuje tylko certyfikaty usługi Key Vault przechowywane w sekcji wpisów tajnych. Importowanie certyfikatu nie powiedzie się, jeśli przechowujesz go w sekcji certyfikatów zamiast w sekcji wpisów tajnych.
> </br> — Usługa Azure Front Door Service obecnie obsługuje tylko certyfikaty przekazywane z plikiem PFX **bez** hasła.

#### <a name="register-azure-front-door-service"></a>Rejestrowanie usługi Azure Front Door Service

Zarejestruj jednostkę usługi dla usługi Azure Front Door Service jako aplikację w usłudze Azure Active Directory za pomocą programu PowerShell.

1. Jeśli to konieczne, zainstaluj program [Azure PowerShell](/powershell/azure/install-az-ps) za pomocą programu PowerShell na komputerze lokalnym.

2. W programie PowerShell uruchom następujące polecenie:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-service-access-to-your-key-vault"></a>Udzielanie usłudze Azure Front Door Service dostępu do magazynu kluczy
 
Udziel usłudze Azure Front Door Service uprawnień dostępu do certyfikatów w obszarze wpisów tajnych na koncie usługi Azure Key Vault.

1. Na koncie magazynu kluczy w obszarze USTAWIENIA wybierz pozycję **Zasady dostępu**, a następnie wybierz pozycję **Dodaj nową**, aby utworzyć nową zasadę.

2. W obszarze **Wybierz podmiot zabezpieczeń** wyszukaj wartość **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** i wybierz pozycję **Microsoft.Azure.Frontdoor**. Kliknij pozycję **Wybierz**.


3. W obszarze **Uprawnienia klucza tajnego** wybierz pozycję **Pobierz**, aby zezwolić usłudze Front Door na użycie tych uprawnień do pobrania i wyświetlenia certyfikatów. 

4. Kliknij przycisk **OK**. 

    Usługa Azure Front Door Service ma teraz dostęp do tego magazynu kluczy i certyfikatów (kluczy tajnych), które są przechowywane w tym magazynie kluczy.
 
#### <a name="select-the-certificate-for-azure-front-door-service-to-deploy"></a>Wybieranie certyfikatu do wdrożenia dla usługi Azure Front Door Service
 
1. Wróć do usługi Front Door w portalu. 

2. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    Zostanie wyświetlona strona **Domena niestandardowa**.

3. W obszarze Typ zarządzania certyfikatami wybierz pozycję **Użyj własnego certyfikatu**. 

4. Usługa Azure Front Door Service wymaga, aby subskrypcja konta usługi Key Vault była taka sama jak dla usługi Front Door. Wybierz magazyn kluczy, certyfikat (klucz tajny) i wersję certyfikatu.

    Usługa Azure Front Door Service zwróci następujące informacje: 
    - Konta magazynów kluczy dla Twojego identyfikatora subskrypcji. 
    - Certyfikaty (klucze tajne) dla wybranego magazynu kluczy. 
    - Dostępne wersje certyfikatów. 
 
5. Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana. Przejdź do sekcji [Oczekiwanie na propagację](#wait-for-propagation).

## <a name="validate-the-domain"></a>Weryfikowanie domeny

Jeśli używasz już domeny niestandardowej mapowanej na niestandardowy punkt końcowy z rekordu CNAME lub własnego certyfikatu, przejdź do sekcji  
[Domena niestandardowa została zamapowana do usługi Front Door](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). W przeciwnym razie, jeśli wpis rekordu CNAME dla Twojej domeny już nie istnieje lub zawiera domenę podrzędną afdverify, przejdź do sekcji [Domena niestandardowa nie została zamapowana do usługi Front Door](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Domena niestandardowa została zamapowana do usługi Front Door według rekordu CNAME

Gdy domena niestandardowa była dodawana do hostów frontonu usługi Front Door, został utworzony rekord CNAME w tabeli DNS rejestratora domen w celu zamapowania go na domyślną nazwę hosta .azurefd.net dla usługi Front Door. Jeśli ten rekord CNAME nadal istnieje i nie zawiera domeny podrzędnej afdverify, urząd certyfikacji DigiCert użyje go automatycznie do zweryfikowania własności domeny niestandardowej. 

Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana.

Rekord CNAME powinien mieć następujący format, gdzie *Nazwa* to nazwa domeny niestandardowej, a *Wartość* to domyślna nazwa hosta .azurefd.net usługi Front Door:

| Name (Nazwa)            | Typ  | Wartość                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz [Tworzenie rekordu DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Jeśli rekord CNAME na poprawny format, firma DigiCert weryfikuje nazwę domeny niestandardowej i tworzy dedykowany certyfikat dla nazwy domeny. Firma DigitCert nie wysyła weryfikacyjnej wiadomości e-mail i nie trzeba zatwierdzać swojego żądania. Certyfikat jest ważny przez jeden rok i jest automatycznie odnawiany przed wygaśnięciem. Przejdź do sekcji [Oczekiwanie na propagację](#wait-for-propagation). 

Automatyczna walidacja przeważnie trwa kilka minut. Jeśli w ciągu godziny nie zobaczysz zweryfikowanej domeny, otwórz bilet pomocy technicznej.

>[!NOTE]
>Jeśli u swojego dostawcy DNS masz rekord autoryzacji urzędu certyfikacji (CAA, Certificate Authority Authorization), musi on zawierać firmę DigiCert jako prawidłowy urząd certyfikacji. Rekord CAA umożliwia właścicielom domen określanie za pomocą dostawców DNS, które urzędy certyfikacji będą upoważnione do wystawiania certyfikatów dla danej domeny. Jeśli urząd certyfikacji otrzymuje zamówienie na certyfikat dla domeny, która ma rekord CAA, a ten urząd certyfikacji nie jest wymieniony jako autoryzowany wystawca, nie może wystawić certyfikatu dla tej domeny ani domeny podrzędnej. Informacje o zarządzaniu rekordami CAA można znaleźć w temacie [Manage CAA records (Zarządzanie rekordami CAA)](https://support.dnsimple.com/articles/manage-caa-record/). Informacje o narzędziu obsługi rekordów CAA można znaleźć w temacie [CAA Record Helper (Pomocnik rekordów CAA)](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Domena niestandardowa nie została zamapowana do usługi Front Door

Jeśli wpis rekordu CNAME dla punktu końcowego już nie istnieje lub zawiera domenę podrzędną afdverify, postępuj zgodnie z dalszymi instrukcjami w tym kroku.

Po włączeniu protokołu HTTPS w domenie niestandardowej urząd certyfikacji DigiCert weryfikuje własność domeny, kontaktując się z jej rejestratorem zgodnie z informacjami o rejestratorze domeny z bazy danych [WHOIS](http://whois.domaintools.com/). Kontakt jest nawiązywany za pośrednictwem adresu e-mail (domyślnie) lub numeru telefonu wymienionego w rejestracji WHOIS. Zanim protokół HTTPS zostanie uaktywniony w domenie niestandardowej, należy ukończyć walidację domeny. Domenę należy zatwierdzić w ciągu sześciu dni roboczych. Żądania, które nie zostały zatwierdzone w ciągu sześciu dni roboczych, zostaną automatycznie anulowane. 

![Rekord WHOIS](./media/front-door-custom-domain-https/whois-record.png)

Firma DigiCert wysyła również weryfikacyjną wiadomość e-mail na dodatkowe adresy e-mail. Jeśli informacje o rejestratorze WHOIS są prywatne, sprawdź, czy możesz zatwierdzić bezpośrednio z jednego z następujących adresów:

admin@&lt;nazwa-domeny.com&gt;  
administrator@&lt;nazwa-domeny.com&gt;  
webmaster@&lt;nazwa-domeny.com&gt;  
hostmaster@&lt;nazwa-domeny.com&gt;  
postmaster@&lt;nazwa-domeny.com&gt;  

W ciągu kilku minut otrzymasz wiadomość e-mail (podobną do poniższego przykładu) z prośbą o zatwierdzenie żądania. Jeśli używasz filtru spamu, dodaj admin@digicert.com do jego listy dozwolonych. Jeśli w ciągu 24 godzin nie otrzymasz wiadomości e-mail, skontaktuj się z działem pomocy technicznej firmy Microsoft.

Po kliknięciu linku zatwierdzania następuje przekierowanie do formularza zatwierdzania online. Postępuj zgodnie z instrukcjami w formularzu. Dostępne są dwie opcje weryfikacji:

- Możesz zatwierdzać wszystkie przyszłe zamówienia składane za pośrednictwem tego samego konta dla tej samej domeny głównej; na przykład contoso.com. Takie podejście jest zalecane, jeśli zamierzasz dodawać kolejne domeny niestandardowe dla tej samej domeny głównej.

- Możesz zatwierdzać tylko nazwę określonego hosta używaną w tym żądaniu. W przypadku kolejnych żądań będzie wymagane dodatkowe zatwierdzanie.

Po zatwierdzeniu firma DigiCert kończy tworzenie certyfikatu dla niestandardowej nazwy domeny. Certyfikat jest ważny przez jeden rok i jest automatycznie odnawiany przed wygaśnięciem.

## <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po zweryfikowaniu nazwy domeny aktywowanie funkcji protokołu HTTPS domeny niestandardowej może potrwać 6–8 godzin. Po ukończeniu procesu stan niestandardowego protokołu HTTPS w witrynie Azure Portal jest ustawiony na **Włączono**, a cztery kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa jest teraz gotowa do korzystania z protokołu HTTPS.

### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku włączania protokołu HTTPS. Po włączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej są wyświetlane cztery kroki operacji. W miarę uaktywniania kolejnych kroków pod każdym wykonywanym aktualnie krokiem są wyświetlane szczegóły dodatkowego kroku podrzędnego. Nie wszystkie kroki podrzędne zostaną wykonane. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Krok operacji | Szczegóły kroku podrzędnego operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| | Trwa przesyłanie żądania HTTPS. |
| | Żądanie HTTPS zostało pomyślnie przesłane. |
| 2 Walidacja domeny | Domena jest automatycznie weryfikowana w celu sprawdzenia, czy jej rekord CNAME jest zamapowany do domyślnego hosta frontonu .azurefd.net dla usługi Front Door. W przeciwnym razie żądanie weryfikacji zostanie wysłane na adres e-mail z listy w rekordzie rejestracji domeny (rejestrator WHOIS). Jak najszybciej zweryfikuj domenę. |
| | Własność domeny została pomyślnie zweryfikowana. |
| | Żądanie weryfikacji własności domeny wygasło (prawdopodobnie klient nie odpowiedział w ciągu 6 dni). Protokół HTTPS nie zostanie włączony w domenie. * |
| | Żądanie weryfikacji własności klienta zostało odrzucone przez klienta. Protokół HTTPS nie zostanie włączony w domenie. * |
| 3 Aprowizowanie certyfikatu | Trwa wystawianie przez urząd certyfikacji certyfikatu wymaganego do włączenia obsługi protokołu HTTPS w domenie. |
| | Certyfikat został wystawiony i trwa jego wdrażanie w usłudze Front Door. Ten proces może potrwać do 1 godziny. |
| | Certyfikat został pomyślnie wdrożony w usłudze Front Door. |
| 4 Ukończenie | Obsługa protokołu HTTPS została pomyślnie włączona w domenie. |

\* Ten komunikat jest wyświetlany tylko w przypadku wystąpienia błędu. 

Jeśli przed przesłaniem żądania wystąpi błąd, zostanie wyświetlony następujący komunikat o błędzie:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Oczyszczanie zasobów — wyłączanie protokołu HTTPS

W poprzednich krokach protokół HTTPS został włączony w domenie niestandardowej. Jeśli nie chcesz już używać domeny niestandardowej z protokołem HTTPS, możesz wyłączyć protokół HTTPS, wykonując poniższe kroki:

### <a name="disable-the-https-feature"></a>Wyłączanie funkcji protokołu HTTPS 

1. W [witrynie Azure Portal](https://portal.azure.com) przejdź do konfiguracji **usługi Azure Front Door Service**.

2. Na liście hostów serwera frontonu kliknij domenę niestandardową, dla której chcesz wyłączyć protokół HTTPS.

3. Kliknij pozycję **Wyłączony**, aby wyłączyć protokół HTTPS, a następnie kliknij pozycję **Zapisz**.

### <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po wyłączeniu funkcji protokołu HTTPS w domenie niestandardowej wprowadzenie tej zmiany może potrwać 6–8 godzin. Po ukończeniu procesu stan niestandardowego protokołu HTTPS w witrynie Azure Portal jest ustawiony na **Wyłączono**, a trzy kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa nie może już używać protokołu HTTPS.

#### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku wyłączenia protokołu HTTPS. Po wyłączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej są wyświetlane trzy kroki operacji. W miarę uaktywniania kolejnych kroków pod każdym krokiem są wyświetlane dodatkowe szczegóły. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Postęp operacji | Szczegóły operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| 2 Anulowanie aprowizacji certyfikatu | Usuwanie certyfikatu |
| 3 Kończenie | Certyfikat został usunięty |

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. *Kto jest dostawcą certyfikatów i jaki typ certyfikatu jest używany?*

    Dla domeny niestandardowej jest używany dedykowany/pojedynczy certyfikat dostarczony przez firmę DigiCert. 

2. *Czy używasz protokołu TLS/SSL opartego na protokole IP, czy z rozszerzeniem SNI?*

    Usługa Azure Front Door Service używa protokołu TLS/SSL z rozszerzeniem SNI.

3. *Co zrobić, jeśli nie otrzymam wiadomości e-mail weryfikującej domenę od firmy DigiCert?*

    Jeśli masz wpis CNAME dla domeny niestandardowej, który wskazuje bezpośrednio na nazwę hosta punktu końcowego (i nie używasz nazwy domeny podrzędnej afdverify), nie otrzymasz wiadomości e-mail weryfikującej domenę. Walidacja będzie wykonywana automatycznie. W przeciwnym razie, jeśli nie masz wpisu CNAME i nie otrzymasz wiadomości e-mail w ciągu 24 godzin, skontaktuj się z działem pomocy technicznej firmy Microsoft.

4. *Czy używanie certyfikatu SAN jest mniejsze bezpieczne niż certyfikatu dedykowanego?*
    
    Certyfikat SAN działa zgodnie z tymi samymi standardami szyfrowania i zabezpieczeń, co certyfikat dedykowany. Wszystkie wystawiane certyfikaty SSL używają algorytmu SHA-256, który pozwala na stosowanie rozszerzonych zabezpieczeń serwerów.

5. *Czy muszę mieć rekord autoryzacji urzędu certyfikacji z moim dostawcą DNS?*

    Nie, rekord autoryzacji urzędu certyfikacji nie jest obecnie wymagany. Jeśli jednak istnieje, musi zawierać firmę DigiCert jako prawidłowy urząd certyfikacji.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
