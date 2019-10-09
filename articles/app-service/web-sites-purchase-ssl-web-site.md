---
title: Kupowanie i Konfigurowanie certyfikatu SSL z poziomu platformy Azure — App Service | Microsoft Docs
description: Dowiedz się, jak kupić certyfikat App Service i powiązać go z aplikacją App Service
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: 9569928882bcaaa1d2406c9af1b2197c2ba6e93d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177516"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Kup i skonfiguruj certyfikat SSL dla Azure App Service

W tym samouczku pokazano, jak zabezpieczyć [aplikację App Serviceową](https://docs.microsoft.com/azure/app-service/) lub [aplikację funkcji](https://docs.microsoft.com/azure/azure-functions/) przez utworzenie (zakup) certyfikatu App Service w [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) , a następnie powiązanie go z aplikacją App Service.

> [!TIP]
> Certyfikaty App Service mogą być używane dla wszystkich usług platformy Azure lub innych niż usługi platformy Azure i nie są ograniczone do App Services. W tym celu należy utworzyć lokalną kopię pliku PFX certyfikatu App Service, którego można użyć w dowolnym miejscu. Aby uzyskać więcej informacji, zobacz [Tworzenie lokalnej kopii PFX certyfikat usługi App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/).
>

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

- [Tworzenie aplikacji App Service](/azure/app-service/)
- [Mapowanie nazwy domeny do aplikacji](app-service-web-tutorial-custom-domain.md) lub [kupowanie i Konfigurowanie na platformie Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Uruchom kolejność certyfikatów

Uruchom App Service kolejność certyfikatów na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">stronie tworzenie certyfikat usługi App Service</a>.

![Tworzenie certyfikatu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Skorzystaj z poniższej tabeli, aby skonfigurować certyfikat. Po zakończeniu kliknij przycisk **Utwórz**.

| Ustawienie | Opis |
|-|-|
| Nazwa | Przyjazna nazwa certyfikatu App Service. |
| Nazwa hosta z wydaną domeną | W tym miejscu określ domenę główną. Wystawiony certyfikat zabezpiecza *zarówno* domenę główną, jak i poddomenę `www`. W wystawionym certyfikacie pole Common Name (nazwa pospolita) zawiera domenę główną, a pole Alternatywna nazwa podmiotu zawiera domenę `www`. Aby zabezpieczyć tylko dowolna poddomena, określ w pełni kwalifikowaną nazwę domeny podrzędnej domeny (na przykład `mysubdomain.contoso.com`).|
| Ramach | Centrum danych, w którym jest hostowana aplikacja sieci Web. |
| Grupa zasobów | Grupa zasobów, która zawiera certyfikat. Możesz na przykład użyć nowej grupy zasobów lub wybrać tę samą grupę zasobów co App Service aplikacji. |
| Jednostka SKU certyfikatu | Określa typ certyfikatu do utworzenia, czy certyfikat standardowy czy [certyfikat wieloznaczny](https://wikipedia.org/wiki/Wildcard_certificate). |
| Postanowienia prawne | Kliknij, aby potwierdzić, że zgadzasz się z postanowieniami prawnymi. Certyfikaty są uzyskiwane z GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Przechowywanie w Azure Key Vault

Po zakończeniu procesu zakupu certyfikatu należy wykonać kilka dodatkowych kroków, aby można było rozpocząć korzystanie z tego certyfikatu. 

Na stronie [certyfikaty App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) wybierz certyfikat, a następnie kliknij pozycję **Konfiguracja certyfikatu** > **krok 1: Magazyn**.

![Wstaw obraz gotowy do przechowania w KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) to usługa platformy Azure, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Jest to magazyn wybrany dla App Service certyfikatów.

Na stronie **stan Key Vault** kliknij pozycję **Key Vault repozytorium** , aby utworzyć nowy magazyn, lub wybierz istniejący magazyn. Jeśli zdecydujesz się utworzyć nowy magazyn, Skorzystaj z poniższej tabeli, aby skonfigurować magazyn, a następnie kliknij przycisk **Utwórz**.

| Ustawienie | Opis |
|-|-|
| Nazwa | Unikatowa nazwa, która składa się z znaków alfanumerycznych i kresek. |
| Grupa zasobów | Zgodnie z zaleceniem wybierz tę samą grupę zasobów co certyfikat App Service. |
| Lokalizacja | Wybierz tę samą lokalizację, w której znajduje się aplikacja App Service. |
| Warstwa cenowa | Aby uzyskać więcej informacji, zobacz [Azure Key Vault szczegóły cennika](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zasady dostępu| Definiuje aplikacje i dozwolony dostęp do zasobów magazynu. Można skonfigurować ją później, wykonując czynności opisane w sekcji [Udziel kilku aplikacjom dostępu do magazynu kluczy](../key-vault/key-vault-group-permissions-for-apps.md). |
| Dostęp Virtual Network | Ogranicz dostęp do magazynu do określonych sieci wirtualnych platformy Azure. Można skonfigurować ją później, wykonując czynności opisane w sekcji [konfigurowanie Azure Key Vault zapór i sieci wirtualnych](../key-vault/key-vault-network-security.md) |

Po wybraniu magazynu zamknij stronę **repozytorium Key Vault** . Opcja **magazynu** powinna zawierać zielony znacznik wyboru dla sukcesu. Pozostaw otwartą stronę w następnym kroku.

## <a name="verify-domain-ownership"></a>Weryfikuj własność domeny

Na tej samej stronie **konfiguracji certyfikatu** , która została użyta w ostatnim kroku, kliknij pozycję **krok 2. Weryfikacja**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Wybierz **App Service weryfikację**. Ponieważ domena została już zmapowana do aplikacji sieci Web (zobacz [wymagania wstępne](#prerequisites)), została już sprawdzona. Po prostu kliknij przycisk **Weryfikuj** , aby zakończyć ten krok. Kliknij przycisk **Odśwież** , dopóki nie zostanie wyświetlona **zweryfikowana domena certyfikatu** .

> [!NOTE]
> Obsługiwane są cztery typy metod weryfikacji domeny: 
> 
> - **App Service** — najbardziej wygodna opcja, gdy domena została już zmapowana do aplikacji App Service w ramach tej samej subskrypcji. Wykorzystuje fakt, że aplikacja App Servicea już zweryfikowała własność domeny.
> - **Domena** — sprawdź [domenę App Service zakupionej na platformie Azure](manage-custom-dns-buy-domain.md). Platforma Azure automatycznie dodaje rekord TXT weryfikacji dla Ciebie i kończy proces.
> - **Poczta** — Sprawdź domenę, wysyłając wiadomość e-mail do administratora domeny. Instrukcje są dostępne po wybraniu opcji.
> - **Ręcznie** — Sprawdź domenę, używając strony HTML (tylko certyfikat**Standardowy** ) lub rekordu TXT DNS. Instrukcje są dostępne po wybraniu opcji.

## <a name="bind-certificate-to-app"></a>Powiąż certyfikat z aplikacją

W **[Azure Portal](https://portal.azure.com/)** z menu po lewej stronie wybierz pozycję **App Services** >  **@no__t — 5your_ App >** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu SSL** > **Certyfikaty prywatne (pfx)**  > **zaimportować certyfikat usługi App Service**.

![Wstaw obraz certyfikatu importu](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Wybierz certyfikat, który został właśnie zakupiony.

Po zaimportowaniu certyfikatu należy powiązać go z zamapowanej nazwy domeny w aplikacji. Wybierz **powiązania** > **Dodaj powiązanie SSL**. 

![Wstaw obraz certyfikatu importu](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Skorzystaj z poniższej tabeli, aby skonfigurować powiązanie w oknie dialogowym **powiązania SSL** , a następnie kliknij przycisk **Dodaj powiązanie**.

| Ustawienie | Opis |
|-|-|
| Nazw | Nazwa domeny, dla której ma zostać dodane powiązanie SSL. |
| Odcisk palca certyfikatu prywatnego | Certyfikat do powiązania. |
| Typ protokołu SSL | <ul><li>**SNI SSL** — można dodać wiele powiązań SSL opartych na SNI. Ta opcja zezwala wielu certyfikatom SSL na zabezpieczanie wielu domen na tym samym adresie IP. Większość nowoczesnych przeglądarek (w tym Internet Explorer, Chrome, Firefox i Opera) obsługuje SNI (Znajdź bardziej kompleksową pomoc techniczną dotyczącą przeglądarki w [oznaczanie nazwy serwera](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**Protokół SSL oparty na** protokole IP — można dodać tylko jedno wiązanie SSL oparte na adresie IP. Ta opcja zezwala na zabezpieczenie dedykowanego publicznego adresu IP tylko jednemu certyfikatowi SSL. Po skonfigurowaniu powiązania postępuj zgodnie z instrukcjami w sekcji ponowne [Mapowanie rekordu dla połączenie SSL z adresu IP](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Weryfikuj dostęp do protokołu HTTPS

Aby sprawdzić, czy certyfikat został prawidłowo skonfigurowany, należy odwiedzić aplikację przy użyciu `HTTPS://<domain_name>` zamiast `HTTP://<domain_name>`.

## <a name="rekey-certificate"></a>Certyfikat ponownego tworzenia kluczy

Jeśli uważasz, że zabezpieczenia klucza prywatnego certyfikatu zostały naruszone, możesz naruszyć certyfikat. Na stronie [certyfikaty App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) wybierz certyfikat, a następnie wybierz pozycję Wymień **i zsynchronizuj** po lewej stronie nawigacyjnej.

Kliknij **pozycję** wykluczaj, aby uruchomić proces. Ten proces może potrwać 1-10 minut.

![Wstaw obraz klucza ponownego tworzenia kluczy SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Ponowne utworzenie klucza certyfikatu zestawia certyfikat z nowym certyfikatem wystawionym przez urząd certyfikacji.

Po ukończeniu operacji ponownego tworzenia kluczy kliknij pozycję **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania przestojów aplikacji.

> [!NOTE]
> Jeśli nie klikniesz przycisku **Synchronizuj**, App Service automatycznie zsynchronizuje certyfikat w ciągu 48 godzin.

## <a name="renew-certificate"></a>Odnów certyfikat

Aby włączyć automatyczne odnawianie certyfikatu w dowolnym momencie, wybierz certyfikat na stronie [App Service certyfikaty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) , a następnie kliknij pozycję **Ustawienia automatycznego odnawiania** w lewym okienku nawigacji. Domyślnie certyfikaty App Service mają roczny okres ważności.

Wybierz pozycję **włączone** , a następnie kliknij pozycję **Zapisz**. Certyfikaty mogą rozpoczynać automatyczne odnawianie 60 dni przed wygaśnięciem, jeśli włączono automatyczne odnawianie.

![Odnów certyfikat automatycznie](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Aby zamiast tego ręcznie odnowić certyfikat, kliknij pozycję **odnowienie ręczne**. Możesz poprosić o ręczne odnowienie certyfikatu o 60 dni przed wygaśnięciem.

Po zakończeniu operacji odnawiania kliknij pozycję **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania przestojów aplikacji.

> [!NOTE]
> Jeśli nie klikniesz przycisku **Synchronizuj**, App Service automatycznie zsynchronizuje certyfikat w ciągu 48 godzin.

## <a name="automate-with-scripts"></a>Automatyzacja za pomocą skryptów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Więcej zasobów

* [Wymuszanie protokołu HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Wymuś protokół TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Użyj certyfikatu protokołu SSL w kodzie aplikacji w Azure App Service](app-service-web-ssl-cert-load.md)
* [Często zadawane pytania: certyfikaty App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
