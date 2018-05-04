---
title: Kupowanie i konfigurowanie certyfikatu SSL dla usługi aplikacji Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kupić certyfikat usługi aplikacji i powiązać aplikację usługi aplikacji
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
ms.openlocfilehash: 63592a1a1c20dd25e5eea66d501f26efeaf0cf21
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Kup i skonfiguruj certyfikat SSL dla usługi Azure App Service

Ten samouczek pokazuje, jak zabezpieczyć aplikację sieci web po zakupie certyfikatu SSL dla Twojego  **[usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, bezpiecznie zapisanie jej w [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), i skojarzenie go z domeny niestandardowej.

## <a name="step-1---log-in-to-azure"></a>Krok 1 — Logowanie do platformy Azure

Zaloguj się do portalu Azure pod adresem http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Krok 2 — złóż zamówienie certyfikat SSL

Kolejność certyfikat SSL można umieścić przez utworzenie nowej [certyfikatu usługi aplikacji](https://portal.azure.com/#create/Microsoft.SSL) w **portalu Azure**.

![Tworzenie certyfikatu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Wprowadź przyjazną nazwę w polu **nazwa** certyfikatów dla ustawienia zabezpieczeń SSL, a następnie wprowadź **nazwy domeny**

> [!NOTE]
> Ten krok jest jednym z najważniejszych części procesu zakupu. Upewnij się wprowadzić poprawną nazwę hosta (domena niestandardowych), który chcesz chronić za pomocą tego certyfikatu. **NIE** Dołącz nazwy hosta z sieci Web. 
>

Wybierz użytkownika **subskrypcji**, **grupy zasobów**, i **certyfikatu jednostki SKU**

> [!TIP]
> Certyfikaty usługi aplikacji można używać dla dowolnej platformy Azure lub usługi Azure i nie jest ograniczona do usługi aplikacji. Aby to zrobić, należy utworzyć kopię lokalną PFX certyfikatu usługi aplikacji czy używania go w dowolnej lokalizacji. Aby uzyskać więcej informacji, przeczytaj [tworzenie lokalnych kopii PFX certyfikat usługi aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Krok 3 — magazyn certyfikatów w usłudze Azure Key Vault

> [!NOTE]
> [Magazyn kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) jest usługą platformy Azure, która pomaga w zabezpieczaniu kluczy kryptograficznych i kluczy tajnych używanych przez usługi i aplikacje w chmurze.
>

Po zakończeniu zakupu certyfikatów SSL, należy otworzyć [certyfikaty usługi aplikacji](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) strony.

![Wstaw obraz gotowy do przechowywania w KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Stan certyfikatu jest **"Oczekujące wystawiania"** są kilka więcej czynności należy wykonać przed rozpoczęciem używania tego certyfikatu.

Kliknij przycisk **Konfiguracja certyfikatów** wewnątrz właściwości certyfikatu i kliknij przycisk na **krok 1: przechowywanie** do przechowywania ten certyfikat w magazynie kluczy Azure.

Z **klucza magazynu stanu** kliknij przycisk **klucza magazynu repozytorium** wybrać istniejącego magazynu kluczy w tym certyfikatem **lub Utwórz nowy magazyn kluczy** Aby utworzyć nowy magazyn kluczy w tej samej subskrypcji i grupie zasobów.

> [!NOTE]
> Usługa Azure Key Vault ma minimalny opłat do przechowywania certyfikatu.
> Aby uzyskać więcej informacji, zobacz  **[szczegóły cennika usługi Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Po wybraniu klucza repozytorium magazynu do przechowywania tego certyfikatu, **przechowywania** opcji powinny być widoczne Powodzenie.

![Wstaw obraz sukcesu magazynu w KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Krok 4. Zweryfikuj prawo własności do domeny

Z tej samej **Konfiguracja certyfikatów** używane w kroku 3 kliknij **krok 2: Sprawdź**.

Wybierz domeny preferowaną metodę weryfikacji. 

Istnieją cztery typy weryfikację domeny obsługiwane przez certyfikaty usługi aplikacji: App Service, domeny poczty i ręczne weryfikacji. Te typy weryfikacji omówiono bardziej szczegółowo w [zaawansowane sekcji](#advanced).

> [!NOTE]
> **Aplikacja usługi weryfikacji** jest najbardziej wygodną opcją w przypadku domeny, aby sprawdzić, jest już zamapowana do aplikacji usługi app Service w tej samej subskrypcji. Go wykorzystuje fakt aplikację usługi aplikacji już zweryfikował własność domeny.
>

Polecenie **Sprawdź** przycisk, aby ukończyć ten krok.

![Wstaw obraz weryfikację domeny](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Po kliknięciu przycisku **Sprawdź**, użyj **Odśwież** przycisku do **Sprawdź** opcji powinny być widoczne Powodzenie.

![Wstaw obraz sprawdzić, czy w KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Krok 5. certyfikat Przypisz do aplikacji usługi App Service

> [!NOTE]
> Przed wykonaniem kroków w tej sekcji, muszą mieć skojarzone nazwy domeny niestandardowej z aplikacją. Aby uzyskać więcej informacji, zobacz  **[Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web.](app-service-web-tutorial-custom-domain.md)**
>

W  **[portalu Azure](https://portal.azure.com/)**, kliknij przycisk **usługi aplikacji** opcję z lewej strony.

Kliknij nazwę aplikacji, do której chcesz przypisać ten certyfikat.

W **ustawienia**, kliknij przycisk **ustawienia protokołu SSL**.

Kliknij przycisk **Importowanie certyfikatu usługi aplikacji** i wybierz certyfikat, który można zakupić.

![Wstaw obraz Importowanie certyfikatu](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

W **powiązania ssl** sekcji kliknij na **dodać powiązania**i wybierz nazwę domeny, aby zabezpieczyć za pomocą protokołu SSL i certyfikatu do użycia przy użyciu list rozwijanych. Można również wybrać opcję korzystania **[oznaczenia nazwy serwera (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** lub adresu IP na podstawie protokołu SSL.

![Wstaw obraz z wiązaniami SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Kliknij przycisk **Dodawanie powiązania** Aby zapisać zmiany i Włącz protokół SSL.

> [!NOTE]
> W przypadku wybrania **IP na podstawie SSL** i domeny niestandardowej jest konfigurowana przy użyciu rekordu A, musisz wykonać następujące dodatkowe czynności. Te omówiono bardziej szczegółowo w [zaawansowane sekcji](#Advanced).

W tym momencie powinno być możliwe do odwiedzenia przy użyciu aplikacji `HTTPS://` zamiast `HTTP://` Aby sprawdzić, czy certyfikat został poprawnie skonfigurowany.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Krok 6 — zadania związane z zarządzaniem

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Advanced

### <a name="verifying-domain-ownership"></a>Weryfikowanie własność domeny

Istnieją dwa typy więcej weryfikacji domeny obsługiwane przez certyfikaty usługi aplikacji: poczty i weryfikacja ręcznego.

#### <a name="mail-verification"></a>Weryfikacja za pomocą wiadomości e-mail

Weryfikacja e-mail została już wysłana na adresy E-mail skojarzony z tym domeny niestandardowej.
Aby ukończyć proces weryfikacji wiadomości E-mail, otwórz wiadomości e-mail i kliknij łącze weryfikacji.

![Wstaw obraz Weryfikacja adresu e-mail](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Jeśli konieczne jest ponowne wysłanie wiadomości e-mail weryfikacji, kliknij przycisk **ponowne wysłanie wiadomości E-mail** przycisku.

#### <a name="domain-verification"></a>Weryfikacja domeny

Wybierz tę opcję tylko w przypadku [domeny usługi App Service zakupionym z platformy Azure.](custom-dns-web-site-buydomains-web-app.md). Azure automatycznie dodaje rekord TXT weryfikacji dla Ciebie i kończy proces.

#### <a name="manual-verification"></a>Weryfikacja ręczna

> [!IMPORTANT]
> HTML strony sieci Web weryfikacji (dotyczy tylko wersji Standard certyfikatu)
>

1. Utwórz plik HTML o nazwie **"starfield.html"**

1. Zawartość tego pliku powinna być dokładną nazwę domeny weryfikacji tokenu. (Możesz skopiować token na stronie Stan weryfikacji domeny)

1. Przekazywanie tego pliku w katalogu głównym serwera sieci web hosting domeny `/.well-known/pki-validation/starfield.html`

1. Kliknij przycisk **Odśwież** można zaktualizować stanu certyfikatu po zakończeniu weryfikacji. Może upłynąć kilka minut, aż weryfikacji.

> [!TIP]
> Sprawdź, czy w terminalu przy użyciu `curl -G http://<domain>/.well-known/pki-validation/starfield.html` odpowiedź powinna zawierać `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Weryfikacja rekordu DNS TXT

1. Korzystanie z Menedżera DNS utworzyć rekord TXT na `@` poddomeny o wartości równej tokenu weryfikacji domeny.
1. Kliknij przycisk **"Odśwież"** można zaktualizować stanu certyfikatu po zakończeniu weryfikacji.

> [!TIP]
> Należy utworzyć rekord TXT na `@.<domain>` o wartości `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Przypisania certyfikatu do aplikacji usługi App Service

W przypadku wybrania **IP na podstawie SSL** i domeny niestandardowej jest konfigurowana przy użyciu rekordu A, musisz wykonać następujące dodatkowe czynności:

Po skonfigurowaniu adresów IP na podstawie powiązania SSL, dedykowany adres IP jest przypisany do aplikacji. Ten adres IP można znaleźć w **domeny niestandardowe** strony w obszarze Ustawienia aplikacji, nad **Hostnames** sekcji. Jest on wyświetlany jako **zewnętrzny adres IP**

![Wstaw obraz z protokołu SSL z adresu IP](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Ten adres IP jest inny niż wirtualny adres IP wcześniej użyty do skonfigurowania rekordu A dla domeny. Jeśli są skonfigurowane do używania SNI na podstawie protokołu SSL lub nie są skonfigurowane do używania protokołu SSL, żaden adres jest wymienionych dla tego wpisu.

Korzystając z narzędzi dostarczanych przez rejestratora nazw domen, zmodyfikuj rekordu A dla nazwy domeny niestandardowe wskazywał adres IP z poprzedniego kroku.

## <a name="rekey-and-sync-the-certificate"></a>Ponowne tworzenie klucza i zsynchronizować certyfikatu

Jeśli trzeba będzie ponowne tworzenie klucza certyfikatu, zaznacz **ponowne generowanie kluczy i synchronizacji** opcję **właściwości certyfikatu** strony.

Kliknij przycisk **ponowne tworzenie klucza** przycisk, aby zainicjować proces. Może to potrwać 1 – 10 minut.

![Wstaw obraz ponowne tworzenie klucza protokołu SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Ponowne tworzenie klucza certyfikatu przedstawia certyfikat przy użyciu nowego certyfikatu wystawionego przez urząd certyfikacji.

<a name="notrenewed"></a>
## <a name="why-is-my-ssl-certificate-not-auto-renewed"></a>Dlaczego moja certyfikat SSL nie zostanie automatycznie odnowiony?

Jeśli certyfikat SSL jest skonfigurowany do automatycznego odnawiania, ale nie są automatycznie odnawiane, może być weryfikacji domeny oczekujące. Pamiętaj o następujących kwestiach: 

- GoDaddy, który generuje certyfikaty z usługi aplikacji, wymaga weryfikacji domeny co trzy lata. Administrator domeny otrzymuje wiadomość e-mail co trzy lata można zweryfikować domeny. Niepowodzenie sprawdzania wiadomości e-mail lub sprawdź domenę zapobiega automatycznego odnawiania certyfikatu usługi aplikacji. 
- Wszystkie certyfikaty usługi aplikacji wydane przed 2017 31 marca wymagają reverification domeny w momencie odnawiania dalej (nawet jeśli włączono automatycznego odnawiania certyfikatu). Jest to wynik zmiany w zasadach GoDaddy. Sprawdź pocztę i ukończyć proces weryfikacji jednorazowe domeny, aby kontynuować automatycznego odnawiania certyfikatu usługi aplikacji. 

## <a name="more-resources"></a>Więcej zasobów

* [Wymuszanie protokołu HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112)
* [Użyj certyfikatu SSL w kodzie aplikacji w usłudze Azure App Service](app-service-web-ssl-cert-load.md)
* [— Często zadawane pytania: Certyfikaty usługi aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)