---
title: Kup i skonfiguruj certyfikat SSL dla usługi Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kupić certyfikatu usługi App Service i powiązać ją z aplikacji usługi app Service
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 0c2adcfa4e11e444f66e1a9c04bea6e3d352f117
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077730"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Kup i skonfiguruj certyfikat SSL dla usługi Azure App Service

W tym samouczku pokazano, jak zabezpieczyć aplikację internetową przez zakup certyfikatu SSL dla Twojego  **[usługi Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, bezpieczne zapisanie jej w [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), i kojarzenie go z domeną niestandardową.

## <a name="step-1---sign-in-to-azure"></a>Krok 1. logowanie się w na platformie Azure

Zaloguj się do witryny Azure portal pod http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Krok 2 — złożenie zamówienia certyfikatu SSL

Można złożyć zamówienie certyfikatu SSL przez utworzenie nowego [certyfikatu usługi App Service](https://portal.azure.com/#create/Microsoft.SSL) w **witryny Azure portal**.

![Tworzenie certyfikatu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Podaj przyjazną **nazwa** certyfikatów dla swojej protokołu SSL, a następnie wprowadź **nazwy domeny**

> [!NOTE]
> Ten krok jest jednym z najbardziej krytycznych części procesu zakupu. Upewnij się, że Wprowadź prawidłową nazwę hosta (domeny niestandardowej), którą chcesz chronić za pomocą tego certyfikatu. **NIE** dołączana nazwa hosta, za pomocą WWW. 
>

Wybierz swoje **subskrypcji**, **grupy zasobów**, i **certyfikatu jednostki SKU**

> [!TIP]
> Certyfikaty usługi App Service może służyć do dowolnej platformy Azure lub usługi platformy Azure i nie jest ograniczona do usług aplikacji. Aby to zrobić, należy utworzyć lokalną kopię PFX certyfikatu usługi App Service, w której można go wszędzie tam, gdzie chcesz. Aby uzyskać więcej informacji, przeczytaj [tworzenia lokalną kopię PFX certyfikatu usługi App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Krok 3 - Store certyfikatu w usłudze Azure Key Vault

> [!NOTE]
> [Usługa Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) jest usługą platformy Azure, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje w chmurze i usług.
>

Po zakończeniu zakupu certyfikatu SSL, należy otworzyć [certyfikaty usługi App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) strony.

![Wstaw obraz chcesz przechowywać w KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Stan certyfikatu jest **"Wystawienie w toku"** jak istnieje kilka dodatkowych czynności, które należy wykonać przed rozpoczęciem używania tego certyfikatu.

Kliknij przycisk **konfigurację certyfikatu** wewnątrz właściwości certyfikatu strony, a następnie kliknij pozycję **krok 1: Store** do przechowywania tego certyfikatu w usłudze Azure Key Vault.

Z **klucza magazynu stanu** kliknij **Key Vault repozytorium** do wyboru istniejącego magazynu kluczy w celu przechowywania tego certyfikatu **lub Utwórz nową usługę Key Vault** do utworzenia nowego magazynu kluczy w tej samej subskrypcji i grupie zasobów.

> [!NOTE]
> Usługa Azure Key Vault ma minimalne opłaty za przechowywanie tego certyfikatu.
> Aby uzyskać więcej informacji, zobacz  **[szczegóły cennika usługi Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Po wybraniu repozytorium klucza magazynu do przechowywania tego certyfikatu, **Store** opcja powinny być widoczne sukces.

![Wstaw obraz przedstawiający Powodzenie magazynu w KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Krok 4. weryfikowanie własności domeny

Z tej samej **konfigurację certyfikatu** została użyta w kroku 3 kliknij **krok 2: Sprawdź**.

Wybierz metodę weryfikacji domeny preferowanych. 

Istnieją cztery typy weryfikacji domeny obsługiwane przez certyfikaty usługi App Service: App Service, domeny i Weryfikacja ręczna. Te typy weryfikacji omówiona bardziej szczegółowo w [zaawansowane sekcji](#advanced).

> [!NOTE]
> **Weryfikacja usługi App** to najwygodniejsza opcja, gdy domeny, którą chcesz zweryfikować jest już zamapowana na aplikację usługi App Service w tej samej subskrypcji. Wykorzystuje ona fakt, czy aplikacja usługi App Service ma już zweryfikowana własność domeny.
>

Kliknij pozycję **Sprawdź** przycisk, aby ukończyć ten krok.

![Wstaw obraz weryfikacji domeny](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Po kliknięciu przycisku **Sprawdź, czy**, użyj **Odśwież** przycisk do momentu **Sprawdź, czy** opcja powinny być widoczne sukces.

![Wstaw obraz sprawdzić, czy w KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Krok 5. certyfikat przypisywanie aplikacji usługi App Service

> [!NOTE]
> Przed wykonaniem kroków w tej sekcji, musi mieć skojarzone nazwy domeny niestandardowej z aplikacją. Aby uzyskać więcej informacji, zobacz  **[Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web.](app-service-web-tutorial-custom-domain.md)**
>

W  **[witryny Azure portal](https://portal.azure.com/)**, kliknij przycisk **usługi App Service** opcję z lewej strony.

Kliknij nazwę aplikacji, do której chcesz przypisać ten certyfikat.

W **ustawienia**, kliknij przycisk **ustawienia protokołu SSL**.

Kliknij przycisk **Import certyfikatu usługi App Service** i wybierz właśnie zakupiony certyfikat.

![Wstaw obraz certyfikatu importu](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

W **powiązania ssl** sekcji kliknij na **Dodaj powiązania**i użyj menu rozwijanych, aby wybrać nazwę domeny, aby zabezpieczyć za pomocą protokołu SSL i certyfikat do użycia. Możesz także wybrać, czy ma być używany **[oznaczaniem nazwy serwera (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** lub oparty na Protokole SSL.

![Wstaw obraz powiązania SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Kliknij przycisk **Dodawanie powiązania** Aby zapisać zmiany i włączyć protokół SSL.

> [!NOTE]
> W przypadku wybrania **oparty na Protokole SSL** i domena niestandardowa jest konfigurowana przy użyciu rekordu A, należy wykonać następujące dodatkowe czynności. Omówiona bardziej szczegółowo w [zaawansowane sekcji](#Advanced).

W tym momencie powinno być możliwe do odwiedzenia aplikacji przy użyciu `HTTPS://` zamiast `HTTP://` Aby sprawdzić, czy certyfikat został poprawnie skonfigurowany.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Krok 6 — zadania związane z zarządzaniem

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Advanced

### <a name="verifying-domain-ownership"></a>Weryfikując własność domeny

Istnieją dwa typy weryfikacji domeny obsługiwane przez certyfikaty usługi App service: Weryfikacja domeny i ręcznej weryfikacji.

#### <a name="domain-verification"></a>Weryfikacja domeny

Wybierz tę opcję tylko w przypadku [domenę usługi App Service, zakupionego na platformie Azure.](custom-dns-web-site-buydomains-web-app.md). Platforma Azure automatycznie dodaje rekord weryfikacji TXT dla Ciebie i kończy proces.

#### <a name="manual-verification"></a>Weryfikacja ręczna

> [!IMPORTANT]
> Weryfikacja strony internetowej HTML (działa tylko z wersji Standard certyfikatu)
>

1. Utwórz plik HTML o nazwie **"starfield.html"**

1. Zawartość tego pliku powinna być dokładną nazwę tokenu weryfikacji domeny. (Możesz skopiować token ze strony stanu weryfikacji domeny)

1. Przekaż ten plik w folderze głównym serwera sieci web hosting domeny `/.well-known/pki-validation/starfield.html`

1. Kliknij przycisk **Odśwież** można zaktualizować stanu certyfikatu po zakończeniu weryfikacji. Może upłynąć kilka minut na wykonanie weryfikacji.

> [!TIP]
> Sprawdź, czy w terminalu przy użyciu `curl -G http://<domain>/.well-known/pki-validation/starfield.html` odpowiedź powinna zawierać `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Weryfikacja rekordu DNS TXT

1. Korzystanie z Menedżera DNS utworzyć rekord TXT w `@` poddomeny o wartości równej tokenu weryfikacji domeny.
1. Kliknij przycisk **"Odśwież"** można zaktualizować stanu certyfikatu po zakończeniu weryfikacji.

> [!TIP]
> Należy utworzyć rekord TXT w `@.<domain>` wartością `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Przypisać certyfikat do aplikacji usługi App Service

W przypadku wybrania **oparty na Protokole SSL** i domena niestandardowa jest konfigurowana przy użyciu rekordu A, należy wykonać następujące dodatkowe czynności:

Po skonfigurowaniu adresów IP na podstawie powiązania SSL, dedykowany adres IP jest przypisany do aplikacji. Ten adres IP można znaleźć na **domena niestandardowa** strony w obszarze Ustawienia aplikacji, nad **nazwy hostów** sekcji. Ta opcja jest wyświetlana jako **zewnętrzny adres IP**

![Wstaw obraz połączenie IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Ten adres IP jest inny niż wirtualny adres IP użyte wcześniej, aby skonfigurować rekord A dla domeny. Jeśli są skonfigurowane do używania SNI SSL opartego na lub nie są skonfigurowane do używania protokołu SSL, adres nie ma na liście dla tego wpisu.

Za pomocą narzędzi dostarczanych przez rejestratora nazw domen, zmodyfikuj rekord A dla nazwy domeny niestandardowej do punktu na adres IP z poprzedniego kroku.

## <a name="rekey-and-sync-the-certificate"></a>Wymiana klucza i synchronizacja certyfikatu

Jeśli kiedykolwiek zajdzie potrzeba wymienić certyfikat, wybierz opcję **wymiany klucza i synchronizacja** opcję **właściwości certyfikatu** strony.

Kliknij przycisk **wymiana** przycisk, aby zainicjować proces. Ten proces może potrwać 1 do 10 minut.

![Wstaw obraz wymiany protokołu SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Wymiana klucza certyfikatu przedstawia certyfikat przy użyciu nowego certyfikatu wystawionego przez urząd certyfikacji.

## <a name="renew-the-certificate"></a>Odnów certyfikat

Aby włączyć funkcję automatycznego odnawiania certyfikatu w dowolnym momencie, kliknij przycisk **ustawienia automatycznego odnawiania** na stronie zarządzania certyfikatu. Wybierz **na** i kliknij przycisk **Zapisz**. Certyfikaty można uruchomić automatycznie odnowienia 90 dni przed wygaśnięciem, jeśli masz automatyczne odnawianie włączone.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Aby zamiast tego ręcznie odnowić certyfikat, kliknij przycisk **odnowienie ręczne** zamiast tego. Możesz poprosić, aby ręcznie odnowić swój certyfikat 60 dni przed wygaśnięciem.

> [!NOTE]
> Odnowionego certyfikatu nie jest automatycznie powiązany z aplikacją, czy ręcznie odnowić automatyczne odnawianie. Aby powiązać go z aplikacją, zobacz [odnawiania certyfikatów](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

## <a name="more-resources"></a>Więcej zasobów

* [Wymuszanie protokołu HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service](app-service-web-ssl-cert-load.md)
* [— Często zadawane pytania: Certyfikaty usługi App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
