---
title: Skonfiguruj protokół HTTPS na domenę niestandardową Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć lub wyłączyć HTTPS na domenę niestandardową Azure CDN punktu końcowego.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: rli; v-deasim
ms.openlocfilehash: ca3dad18973197f63e69e6568b8ea5988b279e01
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="configure-https-on-an-azure-cdn-custom-domain"></a>Skonfiguruj protokół HTTPS na domenę niestandardową Azure CDN

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Azure sieci dostarczania zawartości (CDN) obsługuje protokół HTTPS dla domeny niestandardowej na punktu końcowego usługi CDN. Przy użyciu protokołu HTTPS na domenę niestandardową, upewnieniu się, że poufne dane jest dostarczany z bezpiecznego za pomocą szyfrowania SSL, gdy są wysyłane przez internet. Protokół HTTPS oferuje zaufania, uwierzytelniania i chroni przed atakami aplikacji sieci web. Ponadto dostarczania zawartości bezpiecznej przy użyciu nazwy domeny (na przykład https:\//www.contoso.com). Przepływ pracy, aby włączyć protokół HTTPS jest uproszczone, za pomocą jednego kliknięcia aktywacji i pełnego zarządzania certyfikatami, wszystkie bez dodatkowych kosztów.

Usługi Azure CDN obsługuje również HTTPS na hosta punktu końcowego CDN, domyślnie. Na przykład w przypadku utworzenia punktu końcowego usługi CDN (taki jak https:\//contoso.azureedge.net), protokół HTTPS jest automatycznie włączone.  

Niektóre z kluczowych atrybutów funkcję HTTPS są:

- Bez dodatkowych kosztów: Brak nie koszty nabycia certyfikatu lub odnowienie i bez dodatkowych kosztów dla ruchu HTTPS. Płacisz tylko za GB wyjście z sieci CDN.

- Włączanie prostego: jednym kliknięciem obsługa jest dostępna z [portalu Azure](https://portal.azure.com). Aby włączyć tę funkcję, można użyć interfejsu API REST lub innych narzędzi dla deweloperów.

- Zakończenie zarządzania certyfikatami: wszystkie certyfikatów nabywania i zarządzania jest już obsługiwane. Certyfikaty są automatycznie udostępniane i odnowione przed jego wygaśnięciem, co spowoduje usunięcie ryzyka przerwanie usługi z powodu certyfikat wygasa.

>[!NOTE] 
>Przed włączeniem obsługi protokołu HTTPS, musi mieć już określone [domenę niestandardową Azure CDN](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Włączanie protokołu HTTPS

Aby włączyć protokół HTTPS na domenę niestandardową, wykonaj następujące kroki:

### <a name="step-1-enable-the-feature"></a>Krok 1: Włącz tę funkcję 

1. W [portalu Azure](https://portal.azure.com), przejdź do Twojej **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu CDN.

2. Na liście punktów końcowych kliknij punkt końcowy zawierający domenę niestandardową.

3. Kliknij przycisk domeny niestandardowej, dla którego chcesz włączyć protokół HTTPS.

    ![Listy domen niestandardowych](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Kliknij przycisk **na** Aby włączyć protokół HTTPS, a następnie przycisk **Zastosuj**.

    ![Stan protokołu HTTPS domeny niestandardowej](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Krok 2: Weryfikowanie domeny

>[!NOTE]
>Jeśli u swojego dostawcy DNS rekord certyfikatu urzędu autoryzacji (CAA), musi on zawierać DigiCert jako prawidłowego urzędu certyfikacji. Rekord CAA umożliwia właścicieli domeny z ich dostawców DNS, które urzędów certyfikacji są upoważnione do wystawiania certyfikatów dla danej domeny. Jeśli urząd certyfikacji otrzyma aby certyfikat dla domeny, która zawiera rekord CAA i urzędu certyfikacji nie jest wymieniony jako autoryzowanego wystawcę, jest zabronione wystawi certyfikat do tej domeny lub poddomeny. Informacje o zarządzaniu CAA rekordów, zobacz [rekordów Zarządzanie CAA](https://support.dnsimple.com/articles/manage-caa-record/). Narzędzia rekordu CAA, zobacz [pomocnika rekordu CAA](https://sslmate.com/caa/).

#### <a name="custom-domain-is-mapped-to-cdn-endpoint"></a>Domena niestandardowa jest mapowany na punkt końcowy CDN

Po dodaniu niestandardową domenę do punktu końcowego utworzony rekord CNAME w tabeli DNS rejestratora domen do mapowania sieci hosta punktu końcowego CDN. Jeśli tego rekordu CNAME nadal istnieje i nie zawiera poddomeny cdnverify, urząd certyfikacji (CA) firmy DigiCert używa go zweryfikować prawo własności do domeny niestandardowej. 

Rekordu CNAME powinna być w następującym formacie, gdzie *nazwa* jest niestandardową nazwę domeny i *wartość* jest Twoje hosta punktu końcowego CDN:

| Name (Nazwa)            | Typ  | Wartość                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz [utworzyć rekord CNAME DNS](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#step-2-create-the-cname-dns-records).

Jeśli Twoje rekord CNAME jest w nieprawidłowym formacie, DigiCert automatycznie sprawdza niestandardową nazwę domeny i dodaje go do nazwy alternatywnej podmiotu (SAN) certyfikatu. DigitCert nie będzie wysyłać wiadomość e-mail z weryfikacji i nie musisz zatwierdzić Twoje żądanie. Certyfikat jest ważny przez jeden rok i był auto odnawiany przed jego wygaśnięciem. Przejdź do [krok 3: Poczekaj, aż propagacji](#step-3-wait-for-propagation). 

Automatyczne sprawdzanie poprawności zwykle trwa kilka minut. Jeśli nie widzisz domeny zweryfikowane w ciągu jednej godziny, otwórz bilet pomocy technicznej.

#### <a name="cname-record-is-not-mapped-to-cdn-endpoint"></a>Rekord CNAME nie jest zamapowany na punkt końcowy CDN

Jeśli wpis rekord CNAME dla punktu końcowego już nie istnieje lub zawiera on poddomeny cdnverify, postępuj zgodnie z dalszymi instrukcjami, w tym kroku.

Po włączeniu HTTPS na domenę niestandardową, urząd certyfikacji (CA) firmy DigiCert weryfikuje prawo własności do domeny kontaktując się z jego rejestratorem zgodnie z domeny [WHOIS](http://whois.domaintools.com/) rejestratorem informacji. Skontaktuj się z zostało utworzone za pomocą adresu e-mail (przez ustawienie domyślne) lub numer telefonu wymienionych w rejestracji WHOIS. Należy ukończyć weryfikacji domeny na domenę niestandardową HTTPS będzie aktywny. Należy zatwierdzić domeny sześć dni roboczych. Żądania, które nie zostały zatwierdzone w ciągu sześciu dni roboczych, zostaną automatycznie anulowane. 

![Rekord WHOIS](./media/cdn-custom-ssl/whois-record.png)

DigiCert również wysyła wiadomość e-mail z weryfikacji na adresy e-mail dodatkowych. Informacji rejestratorem WHOIS jest prywatny, sprawdź, czy możesz zatwierdzić bezpośrednio z jednego z następujących adresów:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

Powinien zostać wyświetlony wiadomości e-mail za kilka minut, podobnie do poniższego przykładu prośbą o zatwierdzenie żądania. Jeśli używasz filtru spamu, Dodaj admin@digicert.com jego listą dozwolonych adresów IP. Jeśli w ciągu 24 godzin nie otrzymasz wiadomość e-mail, skontaktuj się z pomocą techniczną firmy Microsoft.
    
![Domeny weryfikacji w wiadomości e-mail](./media/cdn-custom-ssl/domain-validation-email.png)

Po kliknięciu łącza zatwierdzenia są kierowane do następującej postaci online zatwierdzenia: 
    
![Formularz Sprawdzanie poprawności domeny](./media/cdn-custom-ssl/domain-validation-form.png)

Postępuj zgodnie z instrukcjami na formularzu. dostępne są dwie opcje weryfikacji:

- Możesz zatwierdzać wszystkie przyszłe zamówień za pomocą tego samego konta dla tej samej domeny głównej; na przykład contoso.com. Takie podejście jest zalecane, jeśli zamierzasz dodać dodatkowe domeny niestandardowej dla tej samej domeny głównej.

- Możesz zatwierdzać tylko nazwę określonego hosta, używany w tym żądaniu. Dodatkowe zatwierdzenia jest wymagana dla kolejnych żądań.

Po zatwierdzeniu żądania DigiCert dodaje certyfikat SAN z niestandardowej nazwy domeny. Certyfikat jest ważny przez jeden rok i był auto odnawiany przed wygasła.

### <a name="step-3-wait-for-propagation"></a>Krok 3: Poczekaj, aż propagacji

Po zweryfikowaniu nazwy domeny może potrwać do 6-8 godzin dla domeny niestandardowej funkcji HTTPS do aktywacji. Po zakończeniu procesu niestandardowych stan HTTPS w portalu Azure jest ustawiony na **włączone** i operacji cztery kroki w oknie dialogowym domeny niestandardowej są oznaczone jako zakończone. Domena niestandardowa jest teraz gotowy do używania protokołu HTTPS.

![Włącz protokół HTTPS w oknie dialogowym](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji, gdy włączyć protokół HTTPS. Po włączeniu HTTPS cztery kroki operacji są wyświetlane w oknie dialogowym domeny niestandardowej. Ponieważ każdy krok staje się aktywny, punkcie dodatkowe szczegóły są wyświetlane w polu krok zgodnie z jego postępów. Nie wszystkie te podetapów zostanie przeprowadzona. Po pomyślnym zakończeniu kroku, zostanie wyświetlony zielony znacznik wyboru obok niej. 

| Operacja kroku | Szczegóły punkcie operacji | 
| --- | --- |
| Żądanie Submitting 1 | Przesyłanie żądania |
| | Trwa przesyłanie żądania HTTPS. |
| | Żądanie HTTPS zostało pomyślnie przesłane. |
| 2 weryfikacji domeny | Domeny automatycznie została sprawdzona, jeśli jest zamapowany na punkt końcowy CDN CNAME. W przeciwnym razie żądania weryfikacji będą wysyłane na adres e-mail w danej domenie rekord rejestracji (WHOIS rejestratorem) na liście. Sprawdź, czy domena tak szybko, jak to możliwe. |
| | Własność domeny została pomyślnie zweryfikowana. |
| | Żądanie sprawdzania poprawności własność domeny wygasło (klienta, prawdopodobnie nie odpowiedział w ciągu 6 dni). Protokół HTTPS nie zostaną włączone w domenie. * |
| | Domeny własność weryfikacji żądanie zostało odrzucone przez klienta. Protokół HTTPS nie zostaną włączone w domenie. * |
| Inicjowanie obsługi certyfikatów 3 | Trwa wystawianie przez urząd certyfikacji certyfikatu wymaganego do włączenia obsługi protokołu HTTPS w domenie. |
| | Certyfikat został wystawiony i jest aktualnie wdrażany do sieci CDN. Może to potrwać do 6 godzin. |
| | Certyfikat został pomyślnie wdrożony w sieci CDN. |
| Zakończ 4 | Obsługa protokołu HTTPS została pomyślnie włączona w domenie. |

\* Ten komunikat jest wyświetlany tylko wystąpił błąd. 


Jeśli wystąpi błąd przed przesłaniem żądania, zostanie wyświetlony następujący komunikat o błędzie:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>Wyłączanie protokołu HTTPS

Po włączeniu HTTPS na domenę niestandardową, można je później wyłączyć. Aby wyłączyć protokół HTTPS, wykonaj następujące kroki:

### <a name="step-1-disable-the-feature"></a>Krok 1: Wyłączanie funkcji 

1. W [portalu Azure](https://portal.azure.com), przejdź do Twojej **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu CDN.

2. Na liście punktów końcowych kliknij punkt końcowy zawierający domenę niestandardową.

3. Kliknij przycisk domeny niestandardowej, dla której chcesz wyłączyć protokołu HTTPS.

    ![Listy domen niestandardowych](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Kliknij przycisk **poza** Aby wyłączyć protokół HTTPS, a następnie przycisk **Zastosuj**.

    ![Niestandardowe okno protokołu HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Krok 2: Poczekaj, aż propagacji

Po wyłączeniu funkcji HTTPS domeny niestandardowej, może upłynąć do 6-8 godzin zostały wprowadzone. Po zakończeniu procesu niestandardowych stan HTTPS w portalu Azure jest ustawiony na **wyłączone** i operacji trzy kroki w oknie dialogowym domeny niestandardowej są oznaczone jako zakończone. Domeny niestandardowej nie można już używać protokołu HTTPS.

![Wyłącz okno protokołu HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji, gdy wyłączenie protokołu HTTPS. Po wyłączeniu HTTPS trzy kroki operacji są wyświetlane w oknie dialogowym domeny niestandardowej. Ponieważ każdy krok staje się aktywny, dodatkowe szczegóły są wyświetlane w polu kroku. Po pomyślnym zakończeniu kroku, zostanie wyświetlony zielony znacznik wyboru obok niej. 

| Postęp operacji | Szczegóły operacji | 
| --- | --- |
| Żądanie Submitting 1 | Przesyłanie Twojego żądania |
| 2 anulowania obsługi certyfikatów | Usuwanie certyfikatu |
| Zakończ 3 | Certyfikat został usunięty |

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. *Kto jest dostawcę certyfikatów i jakiego typu używanego certyfikatu?*

    Firma Microsoft używa nazwy alternatywnej podmiotu (SAN) certyfikatu firmy DigiCert. Certyfikat SAN można zabezpieczyć wiele nazw FQDN z jednym certyfikatem.

2. *Można użyć dedykowanego certyfikatu?*
    
    Aktualnie nie ale nie znajduje się na plan.

3. *Co zrobić, jeśli I nie otrzymywać wiadomości e-mail weryfikacji domeny firmy DigiCert?*

    Jeśli masz wpis CNAME dla domeny niestandardowej wskazujące bezpośrednio z hosta punktu końcowego (i nie jest używana nazwa poddomeny cdnverify), nie będziesz otrzymywać wiadomość e-mail z domeny weryfikacji. Sprawdzanie poprawności jest wykonywane automatycznie. W przeciwnym razie jeśli nie masz wpis CNAME nie otrzymasz wiadomość e-mail w ciągu 24 godzin, skontaktuj się z pomocy technicznej firmy Microsoft.

4. *Używa mniej bezpieczna niż certyfikat dedykowanych certyfikat SAN?*
    
    Certyfikat SAN wykonuje szyfrowanie i zabezpieczenia standardach jako dedykowane certyfikatu. Wszystkie wystawiane certyfikaty SSL używać algorytmu SHA-256 zabezpieczeń serwera rozszerzonej.

5. *Czy można używać domeny niestandardowej HTTPS z usługi Azure CDN from Akamai?*

    Obecnie ta funkcja jest dostępna tylko z usługą Azure CDN from Verizon. Firma Microsoft pracuje Obsługa tej funkcji w programie Azure CDN from Akamai w najbliższych miesiącach.

6. *Należy rekord certyfikatu urzędu autoryzacji z mojego dostawcy DNS?*

    Nie, rekord certyfikatu urzędu autoryzacji nie jest obecnie wymagane. Jednak jeśli istnieje, musi on zawierać DigiCert jako prawidłowego urzędu certyfikacji.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak skonfigurować [niestandardową domenę na punkt końcowy usługi Azure CDN](./cdn-map-content-to-custom-domain.md)


