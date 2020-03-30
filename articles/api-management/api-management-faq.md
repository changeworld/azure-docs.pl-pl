---
title: Często zadawane pytania dotyczące zarządzania interfejsami API platformy Azure | Dokumenty firmy Microsoft
description: Poznaj odpowiedzi na często zadawane pytania(FAQ), wzorce i najlepsze rozwiązania w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: c32fdc67c74e100e0e31dad3afde128c05c356d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335970"
---
# <a name="azure-api-management-faqs"></a>Często zadawane pytania dotyczące zarządzania interfejsami API platformy Azure
Uzyskaj odpowiedzi na typowe pytania, wzorce i najlepsze rozwiązania dotyczące usługi Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Skontaktuj się z nami
* [Jak zadać pytanie zespołowi zarządzania interfejsami API platformy Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Często zadawane pytania
* [Co to znaczy, że funkcja jest w wersji zapoznawczej?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak mogę zabezpieczyć połączenie między bramą usługi API Management i usługami zaplecza?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak mogę skopiować moje wystąpienie usługi API Management do nowego wystąpienia?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Czy mogę zarządzać wystąpieniem zarządzania interfejsami API programowo?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak dodać użytkownika do grupy administratorów?](#how-do-i-add-a-user-to-the-administrators-group)
* [Dlaczego zasady, które chcę dodać, są niedostępne w edytorze zasad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak skonfigurować wiele środowisk w jednym interfejsie API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Czy mogę używać protokołu SOAP z zarządzaniem interfejsami API?](#can-i-use-soap-with-api-management)
* [Czy mogę skonfigurować serwer autoryzacji OAuth 2.0 z zabezpieczeniami usług AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Jaką metodę routingu usługa API Management jest używana w wdrożeniach do wielu lokalizacji geograficznych?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Czy mogę użyć szablonu usługi Azure Resource Manager do utworzenia wystąpienia usługi zarządzania interfejsami API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Czy mogę używać certyfikatu TLS/SSL z podpisem własnym dla zaplecza?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Dlaczego podczas próby sklonowania repozytorium GIT otrzymuję błąd uwierzytelniania?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Czy usługa Zarządzanie interfejsami API współpracuje z usługą Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Dlaczego wymagamy dedykowanej podsieci w stylu sieci wirtualnych Menedżera zasobów, gdy jest w nich wdrażane zarządzanie interfejsami API?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaki jest minimalny rozmiar podsieci potrzebny podczas wdrażania usługi Api Management w sieci wirtualnej?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Czy mogę przenieść usługę API Management z jednej subskrypcji do innej](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem interfejsu API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Jak zadać pytanie zespołowi zarządzania interfejsami API platformy Microsoft Azure?
Możesz skontaktować się z nami, korzystając z jednej z następujących opcji:

* Zamieść swoje pytania na naszym [forum MSDN Api Management](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Wyślij wiadomość e-mail na adres <mailto:apimgmt@microsoft.com>.
* Wyślij nam prośbę o funkcję na [forum opinii platformy Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co to znaczy, że funkcja jest w wersji zapoznawczej?
Gdy funkcja jest w wersji zapoznawczej, oznacza to, że aktywnie poszukujemy opinii na temat tego, jak ta funkcja działa dla Ciebie. Funkcja w wersji zapoznawczej jest funkcjonalnie kompletna, ale możliwe, że dokonamy przełomowej zmiany w odpowiedzi na opinie klientów. Zaleca się, aby nie zależeć od funkcji, która jest w wersji zapoznawczej w środowisku produkcyjnym. Jeśli masz jakieś opinie na temat funkcji w wersji zapoznawczej, poinformuj nas o tym za pomocą jednej z opcji kontaktu w [jaki sposób mogę zadać pytanie zespołowi zarządzania interfejsami API platformy Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak mogę zabezpieczyć połączenie między bramą usługi API Management i usługami zaplecza?
Istnieje kilka opcji zabezpieczania połączenia między bramą zarządzania interfejsami API a usługami zaplecza. Możesz:

* Użyj uwierzytelniania podstawowego HTTP. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).
* Użyj wzajemnego uwierzytelniania TLS zgodnie z opisem w [jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatów klienta w usłudze Azure API Management](api-management-howto-mutual-certificates.md).
* Użyj białej listy adresów IP w usłudze zaplecza. We wszystkich warstwach zarządzania interfejsami API, z wyjątkiem warstwy Zużycie, adres IP bramy pozostaje stały, z kilkoma zastrzeżeniami opisanymi w [artykule dokumentacji IP](api-management-howto-ip-addresses.md).
* Połącz wystąpienie zarządzania interfejsami API z siecią wirtualną platformy Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak mogę skopiować moje wystąpienie usługi API Management do nowego wystąpienia?
Istnieje kilka opcji, jeśli chcesz skopiować wystąpienie zarządzania interfejsem API do nowego wystąpienia. Możesz:

* Użyj funkcji tworzenia kopii zapasowych i przywracania w programie API Management. Aby uzyskać więcej informacji, zobacz [Jak zaimplementować odzyskiwanie po awarii przy użyciu kopii zapasowej i przywracania usługi w usłudze Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Utwórz własną funkcję tworzenia kopii zapasowych i przywracania przy użyciu [interfejsu API REST zarządzania interfejsem API](/rest/api/apimanagement/). Użyj interfejsu API REST, aby zapisać i przywrócić jednostki z wystąpienia usługi, które chcesz.
* Pobierz konfigurację usługi za pomocą Git, a następnie przekaż ją do nowego wystąpienia. Aby uzyskać więcej informacji, zobacz [Jak zapisać i skonfigurować konfigurację usługi API Management przy użyciu git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Czy mogę zarządzać wystąpieniem zarządzania interfejsami API programowo?
Tak, zarządzanie interfejsem API można zarządzać programowo za pomocą:

* Interfejs [API REST zarządzania interfejsem API](/rest/api/apimanagement/).
* Zestaw [SDK biblioteki zarządzania usługami apimanagement platformy Microsoft Azure](https://aka.ms/apimsdk).
* Polecenia cmdlet [wdrażania usługi](https://docs.microsoft.com/powershell/module/wds) i [zarządzania usługami](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) programu PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak dodać użytkownika do grupy administratorów?
Aby dodać użytkownika do grupy Administratorzy:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do grupy zasobów, która ma wystąpienie zarządzania interfejsami API, które chcesz zaktualizować.
3. W usłudze API Management przypisz do użytkownika rolę **Współautor usługi zarządzania interfejsami** api.

Teraz nowo dodany współautor może używać [poleceń cmdlet](https://docs.microsoft.com/powershell/azure/overview)programu Azure PowerShell. Oto jak zalogować się jako administrator:

1. Użyj `Connect-AzAccount` polecenia cmdlet, aby się zalogować.
2. Ustaw kontekst na subskrypcję, która `Set-AzContext -SubscriptionID <subscriptionGUID>`ma usługę przy użyciu programu .
3. Uzyskaj adres URL logowania jednokrotnego za pomocą programu `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Użyj adresu URL, aby uzyskać dostęp do portalu administracyjnego.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Dlaczego zasady, które chcę dodać, są niedostępne w edytorze zasad?
Jeśli zasady, które chcesz dodać, są wyszarzone lub zacieniowane w edytorze zasad, upewnij się, że masz odpowiedni zakres zasad. Każda instrukcja zasad jest przeznaczona do użycia w określonych zakresach i sekcjach zasad. Aby przejrzeć sekcje zasad i zakresy zasad, zobacz sekcję Użycie zasad w [zasadach zarządzania interfejsami API](/azure/api-management/api-management-policies).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak skonfigurować wiele środowisk w jednym interfejsie API?
Aby skonfigurować wiele środowisk, na przykład środowiska testowego i środowiska produkcyjnego, w jednym interfejsie API, masz dwie opcje. Możesz:

* Hostuj różne interfejsy API w tej samej dzierżawie.
* Hostuj te same interfejsy API w różnych dzierżawach.

### <a name="can-i-use-soap-with-api-management"></a>Czy mogę używać protokołu SOAP z zarządzaniem interfejsami API?
[Obsługa przejścia protokołu SOAP](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) jest już dostępna. Administratorzy mogą importować WSDL swojej usługi SOAP, a usługa Azure API Management utworzy frontonia soap. Dokumentacja portalu dla deweloperów, konsola testowa, zasady i analizy są dostępne dla usług SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Czy mogę skonfigurować serwer autoryzacji OAuth 2.0 z zabezpieczeniami usług AD FS?
Aby dowiedzieć się, jak skonfigurować serwer autoryzacji OAuth 2.0 z zabezpieczeniami usług federacyjnych Active Directory (AD FS), zobacz [Używanie usługi ADFS w zarządzaniu interfejsami API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Jaką metodę routingu usługa API Management jest używana w wdrożeniach do wielu lokalizacji geograficznych?
Usługa API Management używa [metody routingu ruchu wydajności](../traffic-manager/traffic-manager-routing-methods.md#performance) we wdrożeniach do wielu lokalizacji geograficznych. Ruch przychodzący jest kierowany do najbliższej bramy interfejsu API. Jeśli jeden region przejdzie w tryb offline, ruch przychodzący jest automatycznie kierowany do następnej najbliższej bramy. Dowiedz się więcej o metodach routingu w [metodach routingu usługi Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Czy mogę użyć szablonu usługi Azure Resource Manager do utworzenia wystąpienia usługi zarządzania interfejsami API?
Tak. Zobacz szablony szybkiego startu [usługi azure api management service.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Czy mogę używać certyfikatu TLS/SSL z podpisem własnym dla zaplecza?
Tak. Można to zrobić za pośrednictwem programu PowerShell lub bezpośrednio przesyłając go do interfejsu API. Spowoduje to wyłączenie sprawdzania poprawności łańcucha certyfikatów i umożliwi korzystanie z certyfikatów z podpisem własnym lub prywatnie podczas komunikowania się z usługi API Management do usług zaplecza.

#### <a name="powershell-method"></a>Metoda programu Powershell ####
Użyj [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (dla nowego zaplecza) lub [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (dla istniejących zaplecza) poleceń `-SkipCertificateChainValidation` cmdlet programu PowerShell i ustaw parametr na `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metoda bezpośredniej aktualizacji interfejsu API ####
1. Tworzenie [encji wewnętrznej bazy danych](/rest/api/apimanagement/) przy użyciu usługi Api Management.     
2. Ustaw **właściwość skipCertificateChainValidation** na **true**.     
3. Jeśli nie chcesz już zezwalać na certyfikaty z podpisem własnym, usuń encję wewnętrznej bazy danych lub ustaw właściwość **skipCertificateChainValidation** na **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Dlaczego podczas próby sklonowania repozytorium Git otrzymuję komunikat o niepowodzeniu uwierzytelniania?
Jeśli używasz Git Credential Manager lub jeśli próbujesz sklonować repozytorium Git przy użyciu programu Visual Studio, może wystąpić znany problem z okna dialogowego poświadczenia systemu Windows. Okno dialogowe ogranicza długość hasła do 127 znaków i obcina hasło wygenerowane przez firmę Microsoft. Pracujemy nad skróceniem hasła. Na razie użyj Git Bash, aby sklonować swoje repozytorium Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Czy usługa Zarządzanie interfejsami API współpracuje z usługą Azure ExpressRoute?
Tak. Usługa API Management współpracuje z usługą Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Dlaczego wymagamy dedykowanej podsieci w stylu sieci wirtualnych Menedżera zasobów, gdy jest w nich wdrażane zarządzanie interfejsami API?
Wymagania dotyczące dedykowanej podsieci dla usługi API Management pochodzą z faktu, że jest on oparty na modelu wdrażania klasycznej (PAAS V1 layer). Chociaż możemy wdrożyć w sieci wirtualnej Menedżera zasobów (warstwa V2), istnieją konsekwencje tego. Klasyczny model wdrażania na platformie Azure nie jest ściśle połączony z modelem Menedżera zasobów, a więc jeśli utworzysz zasób w warstwie V2, warstwa V1 nie wie o tym i mogą się zdarzyć problemy, takie jak zarządzanie interfejsami API, które próbują użyć adresu IP, który jest już przydzielony do karty sieciowej ( zbudowany na V2).
Aby dowiedzieć się więcej na temat różnicy modeli klasycznego i resource managera na platformie Azure, zapoznaj się [z różnicami w modelach wdrażania.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaki jest minimalny rozmiar podsieci potrzebny podczas wdrażania usługi Api Management w sieci wirtualnej?
Minimalny rozmiar podsieci wymagany do wdrożenia usługi API Management to [/29](../virtual-network/virtual-networks-faq.md#configuration), czyli minimalny rozmiar podsieci, który obsługuje platformę Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Czy mogę przenieść usługę API Management z jednej subskrypcji do innej
Tak. Aby dowiedzieć się, jak to zrobić, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem interfejsu API?
[Znane problemy i ograniczenia](api-management-api-import-restrictions.md) dotyczące formatów Open API(Swagger), WSDL i WADL.
