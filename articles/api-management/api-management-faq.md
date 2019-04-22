---
title: Usługa Azure API Management — często zadawane pytania | Dokumentacja firmy Microsoft
description: Dowiedz się, odpowiedzi na często zadawane pytania (FAQ), wzorców i najlepszych praktyk w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 9c0c8adca9d99c00e32127e02a3d68ff668a235e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793309"
---
# <a name="azure-api-management-faqs"></a>Często zadawane pytania usługi Azure API Management
Odpowiedzi na często zadawane pytania dotyczące, wzorce i najlepsze rozwiązania dotyczące usługi Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Skontaktuj się z nami
* [Jak mogę zadawać zespołu Microsoft Azure API Management pytanie?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Często zadawane pytania
* [Co oznacza Jeśli funkcja jest dostępna w wersji zapoznawczej?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak mogę zabezpieczyć połączenie między bramą usługi API Management i usługami zaplecza?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak mogę skopiować moje wystąpienie usługi API Management do nowego wystąpienia?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Może zarządzać Mój wystąpienia usługi API Management programowo?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak dodać użytkownika do grupy administratorów?](#how-do-i-add-a-user-to-the-administrators-group)
* [Dlaczego jest zasady, które chcę dodać niedostępne w edytorze zasad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak skonfigurować wiele środowisk w pojedynczy interfejs API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Dzięki usłudze API Management można używać protokołu SOAP?](#can-i-use-soap-with-api-management)
* [Jest stałej adresu IP bramy usługi API Management? Można go używać w regułach zapory?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* Przy użyciu zabezpieczeń usług AD FS można skonfigurować serwera autoryzacji OAuth 2.0?
* [Jakie metody routingu dla usługi API Management używa w przypadku wdrożeń w wielu lokalizacjach geograficznych?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Aby utworzyć wystąpienie usługi API Management można używać szablonu usługi Azure Resource Manager?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Czy mogę używać certyfikatu SSL z podpisem własnym dla zaplecza?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Dlaczego komunikat o niepowodzeniu uwierzytelniania, przy próbie sklonowania repozytorium GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Usługa API Management działa z usługą Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Dlaczego firma Microsoft wymagają dedykowanej podsieci w stylu Menedżera zasobów sieci wirtualnych po wdrożeniu usługi API Management do nich?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaki jest rozmiar podsieci minimalne wymagane podczas wdrażania usługi API Management w sieci Wirtualnej?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Czy mogę przenieść usługę API Management z jednej subskrypcji do innej](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem wybór interfejsu API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Jak mogę zadawać zespołu Microsoft Azure API Management pytanie?
Użytkownik może skontaktuj się z nami przy użyciu jednej z następujących opcji:

* Publikuj swoje pytania w naszym [forum API Management MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Wyślij wiadomość e-mail na adres <mailto:apimgmt@microsoft.com>.
* Wyślij zgłoszenie dotyczące funkcji [forum opinii platformy Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co oznacza Jeśli funkcja jest dostępna w wersji zapoznawczej?
Jeśli funkcja jest dostępna w wersji zapoznawczej, oznacza to, że firma Microsoft jest aktywnie znalezienia opinie na temat pracy funkcji dla Ciebie. Funkcja w wersji zapoznawczej jest funkcjonalnie ukończone, ale istnieje możliwość, że wprowadzimy istotne zmiany w odpowiedzi na opinie klientów. Zaleca się, że nie są zależne od funkcji, która jest dostępna w wersji zapoznawczej w środowisku produkcyjnym. Jeśli macie jakieś uwagi na funkcje w wersji zapoznawczej, prosimy o kontakt przez jedną z opcji kontaktu w [jak mogę zadawać zespołu Microsoft Azure API Management pytanie?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak można zabezpieczyć połączenia między bramą usługi API Management i wykaz usług zaplecza
Istnieje kilka opcji, aby zabezpieczyć połączenia między bramą usługi API Management i usług zaplecza. Możesz:

* Stosuj uwierzytelnianie podstawowe HTTP. Aby uzyskać więcej informacji, zobacz [importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).
* Użyj wzajemnego uwierzytelniania protokołu SSL, zgodnie z opisem w [sposób zabezpieczania usług zaplecza przy użyciu klienta uwierzytelniania certyfikatów w usłudze Azure API Management](api-management-howto-mutual-certificates.md).
* Użyj listy dozwolonych adresów IP w usłudze zaplecza. We wszystkich warstwach usługi API Management, adres IP bramy pozostanie niezmieniona, za pomocą kilku [zastrzeżenia](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Możesz ustawić Twojej listy dozwolonych, aby zezwolić na ten adres IP. Na pulpicie nawigacyjnym w witrynie Azure portal, można uzyskać adresu IP wystąpienia usługi API Management.
* Wystąpienie usługi API Management nawiązać połączenia z siecią wirtualną platformy Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak skopiować Moje wystąpienia usługi API Management do nowego wystąpienia?
Istnieje kilka opcji, jeśli chcesz skopiować wystąpienia usługi API Management do nowego wystąpienia. Możesz:

* Użyj funkcji kopii zapasowej i przywracania funkcji w usłudze API Management. Aby uzyskać więcej informacji, zobacz [sposób zaimplementować funkcje odzyskiwania po awarii przy użyciu usługi kopii zapasowej i przywracania w usłudze Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Utwórz własną kopię zapasową i przywrócić funkcji przy użyciu [interfejsu API REST zarządzania interfejsu API](/rest/api/apimanagement/). Zapisywanie i przywracanie jednostki z wystąpienia usługi, która ma za pomocą interfejsu API REST.
* Pobierz konfigurację usługi przy użyciu narzędzia Git, a następnie przekaż go do nowego wystąpienia. Aby uzyskać więcej informacji, zobacz [zapisywanie i konfigurowanie konfiguracji usługi API Management przy użyciu narzędzia Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Może zarządzać Mój wystąpienia usługi API Management programowo?
Tak, można zarządzać usługi API Management programowo przy użyciu:

* [Usługi API Management REST API](/rest/api/apimanagement/).
* [Biblioteki zarządzania usługi ApiManagement platformy Microsoft Azure SDK](https://aka.ms/apimsdk).
* [Wdrożenie usługi](https://docs.microsoft.com/powershell/module/wds) i [Zarządzanie usługami](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) poleceń cmdlet programu PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak dodać użytkownika do grupy administratorów?
Poniżej przedstawiono, jak można dodać użytkownika do grupy administratorów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do grupy zasobów zawierającej wystąpienia usługi API Management, który chcesz zaktualizować.
3. W usłudze API Management, należy przypisać **interfejsu Api zarządzania Współautor** roli do użytkownika.

Teraz używać programu Azure PowerShell nowo dodanych Współautor [poleceń cmdlet](https://docs.microsoft.com/powershell/azure/overview). Oto jak zalogować się jako administrator:

1. Użyj `Connect-AzAccount` polecenia cmdlet, aby zalogować się.
2. Ustaw kontekst subskrypcji, z usługi przy użyciu `Set-AzContext -SubscriptionID <subscriptionGUID>`.
3. Pobierz pojedynczy adres URL logowania za pomocą `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Adres URL umożliwia dostęp do portalu administratora.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Dlaczego jest zasady, które chcę dodać niedostępne w edytorze zasad?
Jeśli zasady, które chcesz dodać pojawi się nieaktywne lub cieniowania w edytorze zasad, upewnij się, że znajdują się w niewłaściwym zakresie zasad. Każda instrukcja zasad jest przeznaczona do użycia w określonych zakresach i sekcji zasady. Aby zapoznać się z sekcji zasad i zakresy dla zasad, zawiera sekcja zasady do użycia w [zasady usługi API Management](/azure/api-management/api-management-policies).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak skonfigurować wiele środowisk w pojedynczy interfejs API?
Aby skonfigurować wielu środowiskach, na przykład środowisko testowe i środowisko produkcyjne, w pojedynczy interfejs API, masz dwie opcje. Możesz:

* Host różnych interfejsów API w tej samej dzierżawy.
* Hostowanie tych samych interfejsów API w różnych dzierżawach.

### <a name="can-i-use-soap-with-api-management"></a>Dzięki usłudze API Management można używać protokołu SOAP?
[Przekazywanie protokołu SOAP](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) obsługa jest teraz dostępna. Administratorzy mogą importować WSDL usługi protokołu SOAP i usługi Azure API Management zostanie tworzenie frontonu protokołu SOAP. Dokumentacja portalu dla deweloperów, konsoli testów, zasad i analizy będą dostępne dla usługi SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Jest stałej adresu IP bramy usługi API Management? Można go używać w regułach zapory?
Publiczny adres IP (VIP) dzierżawcy interfejsu API zarządzania we wszystkich warstwach usługi API Management, jest statyczny okres istnienia dzierżawy, z pewnymi wyjątkami. Zmiana adresu IP w takiej sytuacji:

* Usługa jest usuwana i ponownie utworzona.
* Subskrypcja usługi jest [zawieszone](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) lub [ostrzegany](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (na przykład zgłaszane) i następnie przywrócone.
* Dodawanie lub usuwanie sieci wirtualnej platformy Azure (sieć wirtualną można użyć tylko na dewelopera i warstwa Premium).

Dla wdrożeń w wielu regionach, gdy region jest zwolnione w wyniku, a następnie przywrócone zmiany adresu regionalnej (tylko w warstwie Premium można użyć wdrożenie w wielu regionach).

Dzierżaw warstwy Premium, które są skonfigurowane dla wdrożenia w wielu regionach są przypisywane jeden publiczny adres IP na region.

Na stronie dzierżawy w witrynie Azure portal, możesz uzyskać adres IP (lub adresy w wdrożenie w wielu regionach).

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Przy użyciu zabezpieczeń usług AD FS można skonfigurować serwera autoryzacji OAuth 2.0?
Aby dowiedzieć się, jak skonfigurować serwer autoryzacji OAuth 2.0 przy użyciu zabezpieczeń usługi Active Directory Federation Services (AD FS), zobacz [za pomocą usług AD FS w usłudze API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Jakie metody routingu dla usługi API Management używa w przypadku wdrożeń w wielu lokalizacjach geograficznych?
API Management używa [wydajności metodę routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md#performance) w przypadku wdrożeń w wielu lokalizacjach geograficznych. Ruch przychodzący jest kierowany do najbliższego bramy interfejsu API. Jeśli jeden region przejdzie do trybu offline, ruch przychodzący jest automatycznie kierowany do następnego najbliższej bramy. Dowiedz się więcej na temat metody routingu w [metody routingu w usłudze Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Aby utworzyć wystąpienie usługi API Management można używać szablonu usługi Azure Resource Manager?
Tak. Zobacz [usługa Azure API Management](https://aka.ms/apimtemplate) szablony szybkiego startu.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Dla zaplecza można używać certyfikatu SSL z podpisem własnym?
Tak. Można to zrobić za pomocą programu PowerShell lub przesyłając bezpośrednio do interfejsu API. To spowoduje wyłączenie sprawdzania poprawności łańcucha certyfikatu i pozwala korzystać z podpisem własnym lub prywatnie podpisany certyfikatów, podczas komunikowania się z usługi API Management z usługami zaplecza.

#### <a name="powershell-method"></a>PowerShell — metoda ####
Użyj [ `New-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (dla nowego zaplecza) lub [ `Set-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (w przypadku istniejącego zaplecza) polecenia cmdlet programu PowerShell i ustaw `-SkipCertificateChainValidation` parametr `True`. 

```powershell
$context = New-AApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metoda bezpośrednia aktualizacja interfejsu API ####
1. Tworzenie [zaplecza](/rest/api/apimanagement/) jednostki za pomocą usługi API Management.     
2. Ustaw **skipCertificateChainValidation** właściwości **true**.     
3. Jeśli nie chcesz już nie zezwalaj na certyfikaty z podpisem własnym, Usuń jednostkę wewnętrznej bazy danych lub ustaw **skipCertificateChainValidation** właściwości **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Dlaczego komunikat o niepowodzeniu uwierzytelniania, przy próbie sklonowania repozytorium Git?
Jeśli używasz programu Git Credential Manager lub jeśli chcesz sklonować repozytorium Git w programie Visual Studio, może wystąpić znany problem z okna dialogowego poświadczeń Windows. Okno dialogowe ogranicza długość hasła do 127 znaków, a jego obcina hasło wygenerowane przez Microsoft. Pracujemy nad skracanie hasło. Na razie użyj powłoki Git Bash sklonować repozytorium Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Usługa API Management działa z usługą Azure ExpressRoute?
Tak. Usługa API Management działa z usługą Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Dlaczego firma Microsoft wymagają dedykowanej podsieci w stylu Menedżera zasobów sieci wirtualnych po wdrożeniu usługi API Management do nich?
Wymaganie dedykowanej podsieci dla usługi API Management jest dostarczany z faktu, że jest on oparty na modelu wdrożenia klasycznego (warstwy PAAS w wersji 1). Gdy możemy wdrożyć w sieci Wirtualnej usługi Resource Manager (Warstwa 2), ma skutków. Klasycznego modelu wdrażania na platformie Azure nie jest ściśle powiązany z modelu usługi Resource Manager, a więc jeśli utworzysz zasób w warstwie 2 warstwy V1 nie wie o nim i problemów może się zdarzyć, takich jak API Management próby wykorzystania adresu IP, który został już przydzielony do karty Sieciowej  (w oparciu V2).
Aby dowiedzieć się więcej na temat różnicy modelach klasycznym i usługi Resource Manager na platformie Azure dotyczą [różnic w modelach wdrażania](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaki jest rozmiar podsieci minimalne wymagane podczas wdrażania usługi API Management w sieci Wirtualnej?
Rozmiar podsieci minimalne wymagane do wdrożenia usługi API Management jest [/29](../virtual-network/virtual-networks-faq.md#configuration), czyli rozmiar minimalny podsieci, obsługiwanych na platformie Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Czy mogę przenieść usługi API Management z jednej subskrypcji do innej?
Tak. Aby dowiedzieć się więcej, zobacz temat [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem wybór interfejsu API?
[Znane problemy i ograniczenia](api-management-api-import-restrictions.md) dla API(Swagger) otwartych formatów WSDL i WADL.
