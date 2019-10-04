---
title: Samouczek — Konfigurowanie protokołu HTTPS w domenie niestandardowej Azure CDN | Microsoft Docs
description: W tym samouczku dowiesz się, jak włączyć i wyłączyć protokół HTTPS w domenie niestandardowej punktu końcowego Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5506e4e4bb41725fd8791d3c6e47bb0b94584923
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959435"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Samouczek: Konfigurowanie protokołu HTTPS w domenie niestandardowej Azure CDN

W tym samouczku pokazano, jak włączyć protokół HTTPS dla domeny niestandardowej skojarzonej z punktem końcowym Azure CDN. Korzystając z protokołu HTTPS w domenie niestandardowej (na przykład https: \//www. contoso. com), upewnij się, że poufne dane są bezpiecznie dostarczane za pośrednictwem szyfrowania TLS/SSL, gdy zostanie on wysłany przez Internet. Gdy przeglądarka sieci Web jest połączona z witryną sieci Web za pośrednictwem protokołu HTTPS, sprawdza certyfikat zabezpieczeń witryny sieci Web i weryfikuje, czy jest wystawiona przez uprawniony urząd certyfikacji. Ten proces zapewnia zabezpieczenia i chroni aplikacje sieci Web przed atakami.

Azure CDN domyślnie obsługuje protokół HTTPS na nazwie hosta punktu końcowego usługi CDN. Jeśli na przykład utworzysz punkt końcowy usługi CDN (np. https: \//contoso. azureedge. NET), protokół HTTPS jest automatycznie włączony.  

Niektóre atrybuty klucza niestandardowej funkcji HTTPS są następujące:

- Bez dodatkowych kosztów: nie ma kosztów nabycia lub odnowienia certyfikatu ani dodatkowych kosztów związanych z ruchem HTTPS. Płacisz tylko za GB ruchu wychodzącego z sieci CDN.

- Proste włączenie: Inicjowanie obsługi jednego kliknięcia jest dostępne w [Azure Portal](https://portal.azure.com). Aby włączyć tę funkcję, można także użyć interfejsu API REST lub innych narzędzi programistycznych.

- Dostępne jest pełne zarządzanie certyfikatami: wszystkie zamówienia i zarządzanie nimi są obsługiwane przez użytkownika. Certyfikaty są automatycznie inicjowane i odnawiane przed wygaśnięciem, co eliminuje ryzyko przerwania działania usługi ze względu na wygaśnięcie certyfikatu.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> - Włącz protokół HTTPS w domenie niestandardowej.
> - Korzystanie z certyfikatu zarządzanego przez sieć CDN 
> - Korzystanie z własnego certyfikatu
> - Weryfikowanie domeny
> - Wyłącz protokół HTTPS w domenie niestandardowej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć profil usługi CDN i co najmniej jeden punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie profilu Azure CDN i punktu końcowego](cdn-create-new-endpoint.md).

Ponadto należy skojarzyć Azure CDN domeną niestandardową w punkcie końcowym usługi CDN. Aby uzyskać więcej informacji, zobacz [Samouczek: Dodawanie domeny niestandardowej do punktu końcowego Azure CDN](cdn-map-content-to-custom-domain.md) 

> [!IMPORTANT]
> Certyfikaty zarządzane przez usługę CDN nie są dostępne dla domen głównych lub nadrzędnych. Jeśli Azure CDN domena niestandardowa jest domeną katalogu głównego lub wierzchołka, należy użyć funkcji Przenieś własny certyfikat. 
>

---

## <a name="ssl-certificates"></a>Certyfikaty SSL
Aby włączyć protokół HTTPS w celu bezpiecznego dostarczania zawartości w Azure CDN domenie niestandardowej, należy użyć certyfikatu SSL. Możesz wybrać użycie certyfikatu zarządzanego przez Azure CDN lub użyć własnego certyfikatu.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opcja 1 (domyślnie): Włączanie protokołu HTTPS przy użyciu certyfikatu zarządzanego przez sieć CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

W przypadku korzystania z certyfikatu zarządzanego przez sieć CDN funkcja HTTPS można włączyć za pomocą zaledwie kilku kliknięć. Azure CDN całkowicie obsługuje zadania zarządzania certyfikatami, takie jak zaopatrzenie i odnawianie. Po włączeniu tej funkcji proces rozpocznie się natychmiast. Jeśli domena niestandardowa jest już zamapowana na punkt końcowy usługi CDN, dalsze akcje nie są wymagane. Azure CDN przetworzy kroki i zakończy żądanie automatycznie. Jeśli jednak domena niestandardowa jest mapowana w innym miejscu, należy użyć poczty e-mail do zweryfikowania własności domeny.

Aby włączyć protokół HTTPS w domenie niestandardowej, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)przejdź do **normy Azure CDN firmy Microsoft**, **Azure CDN Standard from Akamai**, **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profile.

2. Na liście punktów końcowych usługi CDN wybierz punkt końcowy zawierający domenę niestandardową.

    ![Lista punktów końcowych](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Zostanie wyświetlona strona **punkt końcowy** .

3. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    ![Lista domen niestandardowych](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Zostanie wyświetlona strona **domena niestandardowa** .

4. W obszarze Typ zarządzania certyfikatami wybierz opcję zarządzane przez usługę **CDN**.

5. Wybierz pozycję **włączone** , aby włączyć protokół https.

    ![Stan protokołu HTTPS domeny niestandardowej](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. [Sprawdź poprawność domeny](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[Opcja 2: Włączanie protokołu HTTPS przy użyciu własnego certyfikatu](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Ta opcja jest dostępna tylko w przypadku **Azure CDN firmy Microsoft** i **Azure CDN z profilów Verizon** . 
>
 
Aby włączyć funkcję HTTPS, można użyć własnego certyfikatu. Ten proces odbywa się za pomocą integracji z usługą Azure Key Vault, co umożliwia bezpieczne przechowywanie certyfikatów. Azure CDN używa tego mechanizmu bezpiecznego do uzyskania certyfikatu i wymaga kilku dodatkowych kroków. Podczas tworzenia certyfikatu SSL należy go utworzyć z dozwolonym urzędem certyfikacji. W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji, żądanie zostanie odrzucone. Aby uzyskać listę dozwolonych urzędów certyfikacji, zobacz [dozwolone urzędy certyfikacji na potrzeby włączania niestandardowego protokołu HTTPS w Azure CDN](cdn-troubleshoot-allowed-ca.md). W przypadku **Azure CDN z Verizon**każdy prawidłowy urząd certyfikacji zostanie zaakceptowany. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Przygotuj swoje konto i certyfikat magazynu kluczy platformy Azure
 
1. Azure Key Vault: musisz mieć uruchomione konto Azure Key Vault w ramach tej samej subskrypcji, co profil Azure CDN i punkty końcowe usługi CDN, dla których chcesz włączyć niestandardowy protokół HTTPS. Utwórz konto Azure Key Vault, jeśli go nie masz.
 
2. Azure Key Vault certyfikaty: Jeśli masz już certyfikat, możesz przekazać go bezpośrednio do konta Azure Key Vault lub można utworzyć nowy certyfikat bezpośrednio za pośrednictwem Azure Key Vault z jednego z urzędów certyfikacji partnera, który Azure Key Vault integruje się z usługą. 

### <a name="register-azure-cdn"></a>Zarejestruj Azure CDN

Zarejestruj Azure CDN jako aplikację w Azure Active Directory za pomocą programu PowerShell.

1. W razie konieczności zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) na komputerze lokalnym.

2. W programie PowerShell uruchom następujące polecenie:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Rejestrowanie Azure CDN w programie PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Udzielanie Azure CDN dostępu do magazynu kluczy
 
Przyznaj Azure CDN uprawnienia dostępu do certyfikatów (wpisów tajnych) na koncie Azure Key Vault.

1. Na koncie magazynu kluczy w obszarze Ustawienia wybierz pozycję **zasady dostępu**, a następnie wybierz pozycję **Dodaj nowe** , aby utworzyć nowe zasady.

    ![Utwórz nowe zasady dostępu](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. W obszarze **Wybierz podmiot zabezpieczeń**Wyszukaj pozycję **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**i wybierz pozycję **Microsoft. Azure. CDN**. Kliknij przycisk **Wybierz**.

    ![Ustawienia zasad dostępu](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Wybierz pozycję **uprawnienia certyfikatów**, a następnie zaznacz pola wyboru dla opcji **Pobierz** i **Wyświetl** , aby umożliwić usłudze CDN wykonywanie tych uprawnień w celu uzyskania i wyświetlenia listy certyfikatów.

4. Wybierz **przycisk OK**. 

    Azure CDN może teraz uzyskiwać dostęp do tego magazynu kluczy i certyfikatów (Secret) przechowywanych w tym magazynie kluczy.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Wybierz certyfikat do Azure CDN do wdrożenia
 
1. Wróć do portalu Azure CDN i wybierz punkt końcowy usługi CDN, w którym chcesz włączyć niestandardowy protokół HTTPS. 

2. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    Zostanie wyświetlona strona **domena niestandardowa** .

3. W obszarze Typ zarządzania certyfikatami wybierz pozycję **Użyj mojego własnego certyfikatu**. 

    ![Konfigurowanie certyfikatu](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Wybierz magazyn kluczy, certyfikat (Secret) i wersję certyfikatu.

    Azure CDN wyświetla następujące informacje: 
    - Konta magazynu kluczy dla identyfikatora subskrypcji. 
    - Certyfikaty (Secret) w wybranym magazynie kluczy. 
    - Dostępne wersje certyfikatów. 
 
5. Wybierz pozycję **włączone** , aby włączyć protokół https.
  
6. W przypadku korzystania z własnego certyfikatu Walidacja domeny nie jest wymagana. [Poczekaj na propagację](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Weryfikowanie domeny

Jeśli masz już domenę niestandardową, która jest zamapowana na niestandardowy punkt końcowy z rekordem CNAME lub używasz własnego certyfikatu, Kontynuuj  
[Domena niestandardowa jest zamapowana na punkt końcowy usługi CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). W przeciwnym razie, Jeśli wpis rekordu CNAME dla punktu końcowego już nie istnieje lub zawiera poddomenę cdnverify, przechodzenie do [domeny niestandardowej nie jest zamapowana na punkt końcowy usługi CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Domena niestandardowa jest zamapowana na punkt końcowy usługi CDN przez rekord CNAME

Po dodaniu domeny niestandardowej do punktu końcowego został utworzony rekord CNAME w tabeli DNS rejestratora domen, aby zamapować go na nazwę hosta punktu końcowego usługi CDN. Jeśli ten rekord CNAME nadal istnieje i nie zawiera poddomeny cdnverify, urząd certyfikacji DigiCert używa go do automatycznego weryfikowania własności domeny niestandardowej. 

Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana.

Rekord CNAME powinien mieć następujący format, gdzie *name* to niestandardowa nazwa domeny i *wartość* jest nazwą hosta punktu końcowego usługi CDN:

| Nazwa            | Typ  | Wartość                 |
|-----------------|-------|-----------------------|
| < www. contoso. com > | CNAME | contoso.azureedge.net |

Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz [Tworzenie rekordu DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Jeśli rekord CNAME ma poprawny format, DigiCert automatycznie weryfikuje swoją niestandardową nazwę domeny i tworzy dedykowany certyfikat dla nazwy domeny. DigitCert nie wyśle Ci weryfikacyjnej wiadomości e-mail i nie będzie trzeba zatwierdzać Twojego żądania. Certyfikat jest ważny przez jeden rok i zostanie przedłużony do autoodnawiania przed jego wygaśnięciem. [Poczekaj na propagację](#wait-for-propagation). 

Automatyczna weryfikacja zwykle trwa kilka godzin. Jeśli nie widzisz zweryfikowanej domeny w ciągu 24 godzin, Otwórz bilet pomocy technicznej.

>[!NOTE]
>Jeśli masz rekord autoryzacji urzędu certyfikacji (CAA) z dostawcą usługi DNS, musi on zawierać DigiCert jako prawidłowy adres klienta. Rekord CAA umożliwia właścicielom domeny Określanie z dostawcami DNS, które urzędy certyfikacji są autoryzowane do wystawiania certyfikatów dla swojej domeny. Jeśli urząd certyfikacji otrzyma zamówienie dla certyfikatu dla domeny, która ma rekord CAA i że urząd certyfikacji nie jest wymieniony jako autoryzowany wystawca, nie można wystawić certyfikatu dla tej domeny lub poddomeny. Informacje o zarządzaniu rekordami CAA można znaleźć w temacie [Manage CAA Records](https://support.dnsimple.com/articles/manage-caa-record/). Aby uzyskać CAA, zobacz [CAA rekordu pomocnika](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Domena niestandardowa nie jest zamapowana na punkt końcowy usługi CDN

>[!NOTE]
>Jeśli używasz **Azure CDN z Akamai**, należy skonfigurować następujący rekord CNAME, aby włączyć automatyczne sprawdzanie poprawności domeny. "_acme-Challenge. &lt;custom domena nazwa hosta @ no__t-1-> CNAME-> &lt;custom domena hostname&gt;.ak-acme-challenge.azureedge.net"

Jeśli wpis rekordu CNAME zawiera poddomenę cdnverify, postępuj zgodnie z pozostałymi instrukcjami w tym kroku.

DigiCert wysyła wiadomość e-mail weryfikacyjną na następujące adresy e-mail. Sprawdź, czy możesz zatwierdzić bezpośrednio z jednego z następujących adresów:

Administrator @ @no__t -0your-Domain-Name. com @ no__t-1  
Administrator @ @no__t -0your-Domain-Name. com @ no__t-1  
webmaster @ @no__t -0your-Domain-Name. com @ no__t-1  
hostmaster @ @no__t -0your-Domain-Name. com @ no__t-1  
postmaster @ @no__t -0your-Domain-Name. com @ no__t-1  

Wiadomość e-mail powinna zostać odebrana w ciągu kilku minut, podobnie jak w poniższym przykładzie, z prośbą o zatwierdzenie żądania. Jeśli używasz filtru spamu, Dodaj verification@digicert.com do jego dozwolonych. Jeśli nie otrzymasz wiadomości e-mail w ciągu 24 godzin, skontaktuj się z pomocą techniczną firmy Microsoft.
    
![Adres e-mail weryfikacji domeny](./media/cdn-custom-ssl/domain-validation-email.png)

Po kliknięciu linku zatwierdzania nastąpi przekierowanie do następującego formularza zatwierdzania w trybie online: 
    
![Formularz weryfikacji domeny](./media/cdn-custom-ssl/domain-validation-form.png)

Postępuj zgodnie z instrukcjami w formularzu; dostępne są dwie opcje weryfikacji:

- Możesz zatwierdzić wszystkie przyszłe zamówienia umieszczone za pomocą tego samego konta dla tej samej domeny głównej; na przykład contoso.com. Ta metoda jest zalecana, jeśli planujesz dodać dodatkowe domeny niestandardowe dla tej samej domeny głównej.

- Możesz zatwierdzić tylko określoną nazwę hosta używaną w tym żądaniu. Dodatkowe zatwierdzenie jest wymagane w przypadku kolejnych żądań.

Po zatwierdzeniu DigiCert kończy Tworzenie certyfikatu dla niestandardowej nazwy domeny. Certyfikat jest ważny przez jeden rok i zostanie przedłużony do jego wygaśnięcia przed jego wygaśnięciem.

## <a name="wait-for-propagation"></a>Poczekaj na propagację

Po sprawdzeniu poprawności nazwy domeny może upłynąć do 6-8 godzin, aby można było aktywować funkcję HTTPS domeny niestandardowej. Po zakończeniu procesu niestandardowy stan protokołu HTTPS w Azure Portal jest ustawiony na **włączone** , a cztery kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa jest teraz gotowa do korzystania z protokołu HTTPS.

![Włącz okno dialogowe HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji, która występuje po włączeniu protokołu HTTPS. Po włączeniu protokołu HTTPS cztery kroki operacji pojawiają się w oknie dialogowym Domena niestandardowa. W miarę jak każdy krok zostanie uaktywniony, dodatkowe szczegóły podetapu pojawią się pod krokiem w miarę postępu. Nie wszystkie z tych podetapów zostaną wykonane. Po pomyślnym ukończeniu kroku obok niego pojawi się zielony znacznik wyboru. 

| Krok operacji | Szczegóły podkroku operacji | 
| --- | --- |
| 1 przesyłane żądanie | Przesyłanie żądania |
| | Trwa przesyłanie żądania HTTPS. |
| | Żądanie HTTPS zostało pomyślnie przesłane. |
| 2 weryfikacja domeny | Domena jest weryfikowana automatycznie, jeśli rekord CNAME jest mapowany na punkt końcowy usługi CDN. W przeciwnym razie żądanie weryfikacji zostanie wysłane do wiadomości e-mail wymienionej w rekordzie rejestracji domeny (WHOIS rejestratorze). Sprawdź domenę najszybciej, jak to możliwe. |
| | Własność domeny została pomyślnie zweryfikowana. |
| | Żądanie weryfikacji własności domeny wygasło (klient nie odpowiedział w ciągu 6 dni). Protokół HTTPS nie zostanie włączony w domenie. * |
| | Żądanie weryfikacji własności domeny zostało odrzucone przez klienta. Protokół HTTPS nie zostanie włączony w domenie. * |
| 3 Inicjowanie obsługi certyfikatów | Urząd certyfikacji aktualnie wystawia certyfikat wymagany do włączenia protokołu HTTPS w domenie. |
| | Certyfikat został wystawiony i jest obecnie wdrażany w sieci CDN. Może to potrwać do 6 godzin. |
| | Certyfikat został pomyślnie wdrożony w sieci CDN. |
| 4 ukończone | Obsługa protokołu HTTPS została pomyślnie włączona w domenie. |

\* ten komunikat nie jest wyświetlany, chyba że wystąpił błąd. 

Jeśli wystąpi błąd przed przesłaniem żądania, zostanie wyświetlony następujący komunikat o błędzie:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Czyszczenie zasobów — wyłączanie protokołu HTTPS

W powyższych krokach włączono protokół HTTPS w domenie niestandardowej. Jeśli nie chcesz już używać domeny niestandardowej z protokołem HTTPS, możesz wyłączyć protokół HTTPS, wykonując następujące czynności:

### <a name="disable-the-https-feature"></a>Wyłącz funkcję HTTPS 

1. W [Azure Portal](https://portal.azure.com)przejdź do **normy Azure CDN firmy Microsoft**, **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profile.

2. Na liście punktów końcowych kliknij punkt końcowy zawierający domenę niestandardową.

3. Kliknij domenę niestandardową, dla której chcesz wyłączyć protokół HTTPS.

    ![Lista domen niestandardowych](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Kliknij przycisk **Wyłącz** , aby wyłączyć protokół https, a następnie kliknij przycisk **Zastosuj**.

    ![Niestandardowe okno dialogowe HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Poczekaj na propagację

Po wyłączeniu funkcji HTTPS domeny niestandardowej może upłynąć do 6-8 godzin. Po zakończeniu procesu niestandardowy stan protokołu HTTPS w Azure Portal jest ustawiony na **wyłączony** , a trzy kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa nie może już korzystać z protokołu HTTPS.

![Wyłącz okno dialogowe HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji, która występuje po wyłączeniu protokołu HTTPS. Po wyłączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej zostaną wyświetlone trzy kroki operacji. W miarę jak każdy krok zostanie uaktywniony, w tym kroku zostaną wyświetlone dodatkowe szczegóły. Po pomyślnym ukończeniu kroku obok niego pojawi się zielony znacznik wyboru. 

| Postęp operacji | Szczegóły operacji | 
| --- | --- |
| 1 przesyłane żądanie | Przesyłanie żądania |
| 2 anulowanie obsługi certyfikatu | Usuwanie certyfikatu |
| 3 zakończone | Usunięto certyfikat |

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. *Kto jest dostawcą certyfikatów i jakiego typu certyfikat jest używany?*

    W przypadku obu **Azure CDN z Verizon** i **Azure CDN firmy Microsoft**w domenie niestandardowej jest używany dedykowany/pojedynczy certyfikat dostarczany przez DigiCert. 

2. *Czy korzystasz z protokołu IP lub SNI TLS/SSL?*

    Obie **Azure CDN od Verizon** i **Azure CDN standard firmy Microsoft** używają SNI TLS/SSL.

3. *Co zrobić, jeśli nie otrzymam wiadomości e-mail weryfikujących domenę z DigiCert?*

    Jeśli masz wpis CNAME dla domeny niestandardowej, która wskazuje bezpośrednio na nazwę hosta punktu końcowego (i nie używasz nazwy domeny podrzędnej cdnverify), nie otrzymasz wiadomości e-mail weryfikującej domenę. Walidacja odbywa się automatycznie. W przeciwnym razie, jeśli nie masz wpisu CNAME i nie otrzymasz wiadomości e-mail w ciągu 24 godzin, skontaktuj się z pomocą techniczną firmy Microsoft.

4. *Czy jest używany certyfikat sieci SAN mniej bezpieczny niż certyfikat dedykowany?*
    
    Certyfikat sieci SAN jest zgodny z tym samym wzorcem szyfrowania i zabezpieczeń, co certyfikat dedykowany. Wszystkie wystawione certyfikaty SSL używają algorytmu SHA-256, aby zwiększyć bezpieczeństwo serwera.

5. *Czy potrzebuję rekordu autoryzacji urzędu certyfikacji z moim dostawcą DNS?*

    Nie. rekord autoryzacji urzędu certyfikacji nie jest obecnie wymagany. Jeśli jednak go masz, musi on zawierać DigiCert jako prawidłowy urząd certyfikacji.

6. *20 czerwca 2018 Azure CDN od Verizon uruchomione przy użyciu dedykowanego certyfikatu z opcją SNI TLS/SSL. Co się stanie z moją istniejącą domeną niestandardową przy użyciu certyfikatu alternatywnej nazwy podmiotu (SAN) i protokołu TLS/SSL opartego na protokole IP?*

    Istniejące domeny zostaną stopniowo migrowane do pojedynczego certyfikatu w nadchodzących miesiącach, jeśli firma Microsoft analizuje tylko SNI żądania klientów w aplikacji. Jeśli firma Microsoft wykryje niektóre żądania klienta inne niż SNI do aplikacji, domeny będą przechowywane w certyfikacie sieci SAN przy użyciu protokołu TLS/SSL opartego na protokole IP. W każdym przypadku nie będzie przerwy w świadczeniu usługi ani pomoc techniczną dla żądań klientów, bez względu na to, czy te żądania są SNI czy SNI.

7. *Jak działają odnowienia certyfikatów przy użyciu pakietu do przenoszenia własnego certyfikatu?*

    Aby zapewnić, że nowszy certyfikat zostanie wdrożony w infrastrukturze PoP, po prostu Przekaż nowy certyfikat do magazynu kluczy platformy Azure, a następnie w ustawieniach protokołu SSL na Azure CDN wybierz najnowszą wersję certyfikatu i kliknij przycisk Zapisz. Azure CDN następnie propogate nowy zaktualizowany certyfikat. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania tych instrukcji:

> [!div class="checklist"]
> - Włącz protokół HTTPS w domenie niestandardowej.
> - Korzystanie z certyfikatu zarządzanego przez sieć CDN 
> - Korzystanie z własnego certyfikatu
> - Sprawdź poprawność domeny.
> - Wyłącz protokół HTTPS w domenie niestandardowej.

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować buforowanie w punkcie końcowym usługi CDN.

> [!div class="nextstepaction"]
> [Samouczek: Ustawianie reguł buforowania Azure CDN](cdn-caching-rules-tutorial.md)

