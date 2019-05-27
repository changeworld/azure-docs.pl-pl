---
title: Kup i skonfiguruj certyfikat SSL na platformie Azure — App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kupić certyfikatu usługi App Service i powiązać ją z aplikacji usługi app Service
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: 0febb8fadd973b67ed232d6094d85894fb383d14
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955713"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Kup i skonfiguruj certyfikat SSL dla usługi Azure App Service

W tym samouczku dowiesz się, jak zabezpieczyć swoje [aplikację usługi app Service](https://docs.microsoft.com/azure/app-service/) lub [aplikacji funkcji](https://docs.microsoft.com/azure/azure-functions/) , tworząc (zakup) certyfikatu usługi App Service w [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) , a następnie wiążą się Aplikacja usługi App Service.

> [!TIP]
> Certyfikaty usługi App Service może służyć do dowolnej platformy Azure lub usługi platformy Azure i nie jest ograniczona do usług aplikacji. Aby to zrobić, należy utworzyć lokalną kopię PFX certyfikatu usługi App Service, w której można go wszędzie tam, gdzie chcesz. Aby uzyskać więcej informacji, zobacz [tworzenia lokalną kopię PFX certyfikatu usługi App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/).
>

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten poradnik:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Mapowanie nazwy domeny na aplikację](app-service-web-tutorial-custom-domain.md) lub [Kup i skonfiguruj go na platformie Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Rozpocznij zamówienia certyfikatu

Rozpocznij zamówienia certyfikatu usługi App Service w <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">certyfikatu usługi App Service, Utwórz stronę</a>.

![Tworzenie certyfikatu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Skorzystaj z poniższej tabeli, aby skonfigurować certyfikat. Po zakończeniu kliknij pozycję **Gotowe**.

| Ustawienie | Opis |
|-|-|
| Name (Nazwa) | Przyjazna nazwa dla certyfikatu usługi App Service. |
| Sama nazwa hosta w domenie | Jeśli określisz domeny katalogu głównego, należy uzyskać certyfikat, który zabezpiecza *zarówno* domeny katalogu głównego i `www` poddomeny. Aby bezpiecznego dowolna poddomena tylko określić w pełni kwalifikowaną nazwę domeny podrzędnej, w tym miejscu (na przykład `mysubdomain.contoso.com`). |
| Subskrypcja | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Grupa zasobów | Grupa zasobów, który zawiera certyfikat. Można użyć nowej grupy zasobów lub wybrać tej samej grupie zasobów jako aplikacji usługi app Service, na przykład. |
| Jednostka SKU certyfikatu | Określa typ certyfikatu w celu utworzenia, czy to standardowy certyfikat lub [certyfikat uniwersalny](https://wikipedia.org/wiki/Wildcard_certificate). |
| Postanowienia prawne | Kliknij, aby upewnić się, że akceptujesz postanowienia prawne. Certyfikaty są uzyskiwane od firmy GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Store w usłudze Azure Key Vault

Po zakończeniu procesu zakupu certyfikatów, istnieje kilka kroków, które należy wykonać przed rozpoczęciem używania tego certyfikatu. 

Wybierz certyfikat w [certyfikaty usługi App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stronie, a następnie kliknij przycisk **konfigurację certyfikatu** > **krok 1: Store**.

![Wstaw obraz chcesz przechowywać w KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Usługa Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) jest usługą platformy Azure, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje w chmurze i usług. Jest magazyn wyborem dla certyfikatów usługi App Service.

W **klucza magazynu stanu** kliknij **Key Vault repozytorium** do utworzenia nowego magazynu, lub wybierz istniejący magazyn. Jeśli zdecydujesz się utworzyć nowy magazyn, skorzystaj z poniższej tabeli ułatwiają konfigurowanie magazynu i kliknij przycisk Utwórz. Zobacz, aby utworzyć nową usługę Key Vault w tej samej subskrypcji i grupie zasobów.

| Ustawienie | Opis |
|-|-|
| Name (Nazwa) | Unikatową nazwę, która składa się na znaki alfanumeryczne i łączniki. |
| Grupa zasobów | Zalecenie wybrać tej samej grupie zasobów certyfikatu usługi App Service. |
| Lokalizacja | Wybierz tej samej lokalizacji co aplikację usługi App Service. |
| Warstwa cenowa | Aby uzyskać informacje, zobacz [szczegóły cennika usługi Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zasady dostępu| Określa aplikacje oraz dozwolony dostęp do zasobów magazynu. Możesz skonfigurować ją później, zgodnie z krokami w [udzielić kilka aplikacji, dostęp do magazynu kluczy](../key-vault/key-vault-group-permissions-for-apps.md). |
| Dostęp do sieci wirtualnej | Ograniczyć dostęp do niektórych sieciach wirtualnych platformy Azure. Możesz skonfigurować ją później, zgodnie z krokami w [skonfigurować Azure Key Vault zapory i sieci wirtualne](../key-vault/key-vault-network-security.md) |

Po wybraniu magazynu Zamknij **Key Vault repozytorium** strony. **Store** opcji powinien zostać wyświetlony zielony znacznik wyboru w celu osiągnięcia sukcesu. Nie zamykaj strony do kolejnego kroku.

## <a name="verify-domain-ownership"></a>Weryfikowanie własności domeny

Z tej samej **konfigurację certyfikatu** używany w ostatnim kroku, kliknij **krok 2: Sprawdź**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Wybierz **Weryfikacja usługi App Service**. Ponieważ domena już zmapowany do aplikacji sieci web (zobacz [wymagania wstępne](#prerequisites)), została już zweryfikowana. Po prostu kliknij **Sprawdź** na zakończenie tego kroku. Kliknij przycisk **Odśwież** przycisk aż komunikat **domena certyfikatu została zweryfikowana** pojawia się.

> [!NOTE]
> Są obsługiwane cztery typy metod weryfikacji domeny: 
> 
> - **Usługa App Service** -najwygodniejsza opcja, jeśli domena jest już zamapowana na aplikację usługi App Service w tej samej subskrypcji. Wykorzystuje ona fakt, czy aplikacja usługi App Service ma już zweryfikowana własność domeny.
> - **Domeny** — Sprawdź [domena usługi App Service, zakupionego na platformie Azure](manage-custom-dns-buy-domain.md). Platforma Azure automatycznie dodaje rekord weryfikacji TXT dla Ciebie i kończy proces.
> - **Poczta** — Sprawdź domenę, wysyłając wiadomość e-mail do administratora domeny. Instrukcje znajdują się po wybraniu opcji.
> - **Ręczne** — weryfikowanie domeny za pomocą strony HTML (**standardowa** tylko certyfikatu) lub rekordu DNS TXT. Instrukcje znajdują się po wybraniu opcji.

## <a name="bind-certificate-to-app"></a>Powiąż certyfikat do aplikacji

W  **[witryny Azure portal](https://portal.azure.com/)**, z menu po lewej stronie wybierz **App Services** > **\<your_ aplikacji >**.

W lewym obszarze nawigacji aplikacji, wybierz **ustawienia protokołu SSL** > **certyfikaty prywatne (.pfx)** > **Import certyfikatu usługi App Service**.

![Wstaw obraz certyfikatu importu](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Wybierz właśnie zakupiony certyfikat.

Teraz, gdy certyfikat został zaimportowany, musisz powiązać go z nazwą domeny mapowane w aplikacji. Wybierz **powiązania** > **Dodaj powiązanie SSL**. 

![Wstaw obraz certyfikatu importu](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Skorzystaj z poniższej tabeli, aby skonfigurować powiązania w **powiązania SSL** okno dialogowe, następnie kliknij przycisk **Dodawanie powiązania**.

| Ustawienie | Opis |
|-|-|
| Nazwa hosta | Nazwa domeny, które można dodać wiązania SSL dla. |
| Odcisk palca certyfikatu prywatnego | Certyfikat, aby powiązać. |
| Typ SSL | <ul><li>**Połączenia SNI SSL** -powiązania SSL oparte na SNI wiele, mogą zostać dodane. Ta opcja umożliwia zabezpieczenie wielu domen na tym samym adresie IP za pomocą wielu certyfikatów protokołu SSL. Większość nowoczesnych przeglądarek (w tym programy Internet Explorer, Chrome, Firefox i Opera) obsługuje funkcję SNI. Bardziej szczegółowe informacje dotyczące obsługi przeglądarek możesz znaleźć w artykule [Server Name Indication (Oznaczanie nazwy serwera)](https://wikipedia.org/wiki/Server_Name_Indication).</li><li>**Połączenie IP SSL** — można dodać tylko jedno powiązanie SSL oparte na protokole IP. Ta opcja umożliwia zabezpieczenie dedykowanego publicznego adresu IP za pomocą tylko jednego certyfikatu protokołu SSL. Po skonfigurowaniu powiązania, wykonaj kroki opisane w [ponowne mapowanie rekordu dla protokołu IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Sprawdź dostęp do protokołu HTTPS

Odwiedź witrynę aplikacji przy użyciu `HTTPS://<domain_name>` zamiast `HTTP://<domain_name>` Aby sprawdzić, czy certyfikat został poprawnie skonfigurowany.

## <a name="rekey-certificate"></a>Wymiana klucza certyfikatu

Jeśli uważasz, że certyfikat użytkownika prywatny klucz zostanie naruszony, możesz wymienić certyfikat. Wybierz certyfikat w [certyfikaty usługi App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stronie, a następnie wybierz **Wymiana klucza i synchronizacja** w lewym obszarze nawigacji.

Kliknij przycisk **wymiana** do rozpoczęcia procesu. Ten proces może potrwać 1 do 10 minut.

![Wstaw obraz wymiany protokołu SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Wymiana klucza certyfikatu przedstawia certyfikat przy użyciu nowego certyfikatu wystawionego przez urząd certyfikacji.

Po zakończeniu operacji ponownego generowania kluczy, kliknij przycisk **synchronizacji**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w usłudze App Service bez powodowania żadnych przestojów aplikacji.

> [!NOTE]
> Jeśli nie klikniesz **synchronizacji**, usługa App Service automatycznie synchronizuje swój certyfikat w ciągu 48 godzin.

## <a name="renew-certificate"></a>Odnów certyfikat

Aby włączyć funkcję automatycznego odnawiania certyfikatu w dowolnym momencie, należy wybrać certyfikat w [certyfikaty usługi App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stronie, a następnie kliknij przycisk **ustawienia automatycznego odnawiania** w nawigacji po lewej stronie.

Wybierz **na** i kliknij przycisk **Zapisz**. Certyfikaty można uruchomić automatyczne odnawianie 60 dni przed wygaśnięciem, w przypadku automatycznego odnawiania włączona.

![automatyczne odnawianie certyfikatu](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Aby zamiast tego ręcznie odnowić certyfikat, kliknij przycisk **odnowienie ręczne**. Możesz poprosić, aby ręcznie odnowić swój certyfikat 60 dni przed wygaśnięciem.

Po zakończeniu operacji Odnów kliknij **synchronizacji**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w usłudze App Service bez powodowania żadnych przestojów aplikacji.

> [!NOTE]
> Jeśli nie klikniesz **synchronizacji**, usługa App Service automatycznie synchronizuje swój certyfikat w ciągu 48 godzin.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Więcej zasobów

* [Enforce HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Enforce TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service](app-service-web-ssl-cert-load.md)
* [FAQ: Certyfikaty usługi App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
