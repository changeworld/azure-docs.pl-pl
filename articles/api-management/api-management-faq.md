---
title: Zarządzanie interfejsami API Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Dowiedz się odpowiedzi na często zadawane pytania (FAQ), w przypadku wzorców i najlepszych praktyk w usłudze Azure API Management.
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
ms.openlocfilehash: 243f0b8206166e3e054dfc423259bd3444d8a2db
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31589836"
---
# <a name="azure-api-management-faqs"></a>Często zadawane pytania usługi Azure API Management
Odpowiedzi na często zadawane pytania, wzorców i najlepsze rozwiązania dotyczące usługi Azure API Management.

## <a name="contact-us"></a>Skontaktuj się z nami
* [Jak można żądanie zespołu Microsoft Azure API Management pytanie?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Często zadawane pytania
* [Co to znaczy, gdy funkcja jest w wersji zapoznawczej?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak można zabezpieczyć połączenie między bramą usługi API Management i Moje usług zaplecza](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak skopiować Moje wystąpienie usługi API Management do nowego wystąpienia?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Może zarządzać Mój wystąpienie interfejsu API zarządzania programowo?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak dodać użytkownika do grupy administratorów?](#how-do-i-add-a-user-to-the-administrators-group)
* [Dlaczego jest zasadę, którą chcę dodać niedostępne w edytorze zasad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak skonfigurować wiele środowisk w jednego interfejsu API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Czy można używać protokołu SOAP z interfejsu API zarządzania?](#can-i-use-soap-with-api-management)
* [To jest stała adres IP bramy usługi API Management? Można jej używać w regułach zapory?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Z zabezpieczeniami usług AD FS można skonfigurować serwera autoryzacji OAuth 2.0?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [W przypadku wdrożeń w wielu lokalizacjach geograficznych jakie metody routingu program używa interfejsu API zarządzania?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Aby utworzyć wystąpienie usługi Zarządzanie interfejsami API mogą używać szablonu usługi Azure Resource Manager?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Czy można użyć certyfikatu SSL z podpisem własnym dla wewnętrznej?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Dlaczego uzyskać wystąpił błąd uwierzytelniania podczas klonowania repozytorium GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Zarządzanie interfejsami API działa z Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Dlaczego, po wdrożeniu usługi API Management do nich, jest wymagamy dedykowanych podsieci w stylu Menedżera zasobów sieci wirtualnych?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Co to jest rozmiar podsieci minimalne podczas wdrażania interfejsu API zarządzania w sieci Wirtualnej?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Można przenieść usługi API Management z jedną subskrypcją do innego?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem Moje interfejsu API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Jak można żądanie zespołu Microsoft Azure API Management pytanie?
Użytkownik może skontaktuj się z nami za pomocą jednej z następujących opcji:

* Opublikuj swoje pytania w naszym [forum API Management MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Wyślij wiadomość e-mail do <mailto:apimgmt@microsoft.com>.
* Wyślij żądanie funkcji [forum opinii Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co to znaczy, gdy funkcja jest w wersji zapoznawczej?
Funkcja jest w wersji zapoznawczej, oznacza, że firma Microsoft jest aktywnie o opinie na jak funkcja działa automatycznie. Funkcja w wersji zapoznawczej jest funkcjonalnie pełne, ale istnieje możliwość, że wybierzemy podziału, zmiany w odpowiedzi na opinie klientów. Zaleca się, że nie są zależne od funkcji, która jest w wersji zapoznawczej w środowisku produkcyjnym. Jeśli masz opinię na funkcje w wersji zapoznawczej, prosimy o kontakt za pomocą jednego z opcjami kontaktu w [jak I poproś zespołu Microsoft Azure API Management pytanie?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak można zabezpieczyć połączenie między bramą usługi API Management i Moje usług zaplecza
Istnieje kilka opcji do bezpiecznego połączenia między bramą usługi API Management i usług zaplecza. Możesz:

* Użyj uwierzytelniania podstawowego HTTP. Aby uzyskać więcej informacji, zobacz [Import i opublikować swój pierwszy interfejs API](import-and-publish.md).
* Używa wzajemnego uwierzytelniania SSL, zgodnie z opisem w [zabezpieczania usług zaplecza za pomocą klienta uwierzytelniania certyfikatów w usłudze Azure API Management](api-management-howto-mutual-certificates.md).
* Użyj listę dozwolonych podobnej IP w usłudze zaplecza. W wszystkie warstwy interfejsu API zarządzania, adres IP bramy pozostaje stała, od kilku [ostrzeżenia](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Można ustawić z listy dozwolonych, aby umożliwić ten adres IP. Adres IP wystąpienia interfejsu API zarządzania można uzyskać na pulpicie nawigacyjnym w portalu Azure.
* Wystąpienie interfejsu API zarządzania nawiązać połączenie sieci wirtualnej platformy Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak skopiować Moje wystąpienie usługi API Management do nowego wystąpienia?
Istnieje kilka opcji, jeśli chcesz skopiować do nowego wystąpienia wystąpienia interfejsu API zarządzania. Możesz:

* Użyj kopii zapasowej i przywracania funkcji w usłudze API Management. Aby uzyskać więcej informacji, zobacz [zaimplementować odzyskiwania po awarii za pomocą usługi tworzenia kopii zapasowej i przywracania w usłudze Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Tworzenie własnych kopii zapasowej i przywracania przy użyciu funkcji [interfejsu API REST zarządzania interfejsu API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Zapisywanie i przywracanie jednostek z wystąpienia usługi, które mają za pomocą interfejsu API REST.
* Pobierz konfigurację usługi przy użyciu narzędzia Git, a następnie przekaż go do nowego wystąpienia. Aby uzyskać więcej informacji, zobacz [sposób zapisania i skonfigurować konfiguracji usługi Zarządzanie interfejsami API przy użyciu narzędzia Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Może zarządzać Mój wystąpienie interfejsu API zarządzania programowo?
Tak, można zarządzać zarządzanie interfejsami API programowo przy użyciu:

* [Interfejsu API REST zarządzania interfejsu API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* [Biblioteka zarządzania ApiManagement usług Microsoft Azure SDK](http://aka.ms/apimsdk).
* [Wdrożenie usługi](https://msdn.microsoft.com/library/mt619282.aspx) i [Zarządzanie usługami](https://msdn.microsoft.com/library/mt613507.aspx) poleceń cmdlet programu PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak dodać użytkownika do grupy administratorów?
Oto, jak dodać użytkownika do grupy administratorów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do grupy zasobów o wystąpienia interfejsu API zarządzania, które chcesz zaktualizować.
3. Zarządzanie interfejsami API przypisywanie **interfejsu Api zarządzania współautora** rolę dla użytkownika.

Teraz używać programu Azure PowerShell nowo dodanego współautora [poleceń cmdlet](https://msdn.microsoft.com/library/mt613507.aspx). Poniżej przedstawiono sposób Zaloguj się jako administrator:

1. Użyj `Connect-AzureRmAccount` polecenia cmdlet do logowania.
2. Ustaw kontekst subskrypcji z usługi przy użyciu `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Pobierz pojedynczy adres URL logowania za pomocą `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Użyj adresu URL do dostępu do portalu administratora.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Dlaczego jest zasadę, którą chcę dodać niedostępne w edytorze zasad?
Jeśli zasady, które chcesz dodać pojawia się nieaktywne lub cieniowania w edytorze zasad, upewnij się, że jesteś w niewłaściwym zakresie zasad. Każda instrukcja zasad jest przeznaczony do użycia w określonych zakresach i sekcje zasad. Aby przejrzeć sekcje zasad i zakresy dla zasad, zobacz sekcję użycia zasad w [zasad interfejsu API zarządzania](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak skonfigurować wiele środowisk w jednego interfejsu API?
Aby skonfigurować wielu środowiskach, na przykład środowiska testowego i środowiska produkcyjnego, w jednym interfejsie API, masz dwie opcje. Możesz:

* Host różnych interfejsów API w tej samej dzierżawy.
* Host tych samych interfejsów API w różnym dzierżawcom.

### <a name="can-i-use-soap-with-api-management"></a>Czy można używać protokołu SOAP z interfejsu API zarządzania?
[Przekazywanie protokołu SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) obsługi jest teraz dostępna. Administratorzy mogą importować WSDL usługi SOAP, a zarządzanie interfejsami API Azure utworzy frontonu protokołu SOAP. Dokumentację portalu dla deweloperów, konsoli testu, zasad i analiza będą dostępne dla usług SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>To jest stała adres IP bramy usługi API Management? Można jej używać w regułach zapory?
Publiczny adres IP (VIP) dla dzierżawcy interfejsu API zarządzania w wszystkie warstwy interfejsu API zarządzania jest statyczny przez czas ich istnienia dzierżawy, z pewnymi wyjątkami. Zmiany adresu IP w takiej sytuacji:

* Usługa jest usunięty i ponownie utworzona.
* Subskrypcja usługi jest [zawieszone](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) lub [ostrzeżona](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (na przykład w przypadku nonpayment), a następnie przywrócone.
* Dodawanie lub usuwanie sieci wirtualnej platformy Azure (sieci wirtualnej można użyć tylko w projektanta i warstwy Premium).

W przypadku wdrożeń, jeśli region jest vacated, a następnie przywrócone zmiany adresu regionalne (możesz użyć w przypadku wdrożenia tylko w warstwie Premium).

Dzierżawcy warstwy Premium, skonfigurowane na potrzeby wdrażania w przypadku przypisano jeden publiczny adres IP dla regionu.

Na stronie dzierżawy w portalu Azure, możesz uzyskać adres IP (lub adresy we wdrożeniu w przypadku).

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Z zabezpieczeniami usług AD FS można skonfigurować serwera autoryzacji OAuth 2.0?
Aby dowiedzieć się, jak skonfigurować serwer autoryzacji OAuth 2.0 z zabezpieczeń usługi Active Directory Federation Services (AD FS), zobacz [za pomocą usług AD FS w usłudze API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>W przypadku wdrożeń w wielu lokalizacjach geograficznych jakie metody routingu program używa interfejsu API zarządzania?
Zarządzanie interfejsami API używa [wydajności Metoda routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md#performance) w przypadku wdrożeń w wielu lokalizacjach geograficznych. Przychodzący ruch jest kierowany do najbliższego bramy interfejsu API. Jeśli jeden region przejdzie do trybu offline, ruch przychodzący automatycznie jest kierowany do następnego najbliższego bramy. Dowiedz się więcej na temat metody routingu w [metody routingu ruchu Menedżera](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Aby utworzyć wystąpienie usługi Zarządzanie interfejsami API mogą używać szablonu usługi Azure Resource Manager?
Tak. Zobacz [usługi Azure API Management](http://aka.ms/apimtemplate) szablony szybkiego startu.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Czy można użyć certyfikatu SSL z podpisem własnym dla wewnętrznej?
Tak. Można to zrobić za pomocą programu PowerShell lub poprzez przesłanie bezpośrednio do interfejsu API. To spowoduje wyłączenie weryfikacji łańcucha certyfikatu i będzie można używać certyfikatów z podpisem własnym lub prywatnie podpisany podczas komunikowania się z interfejsu API zarządzania usługami zaplecza.

#### <a name="powershell-method"></a>PowerShell — metoda ####
Użyj [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (dla nowego zaplecza) lub [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (dla istniejących zaplecza) poleceń cmdlet programu PowerShell i ustaw `-SkipCertificateChainValidation` parametr `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metoda bezpośrednia aktualizacja interfejsu API ####
1. Utwórz [zaplecza](https://msdn.microsoft.com/library/azure/dn935030.aspx) jednostek przy użyciu interfejsu API zarządzania.       
2. Ustaw **skipCertificateChainValidation** właściwości **true**.     
3. Jeśli nie chcesz zezwolić certyfikaty z podpisem własnym, usunąć jednostkę wewnętrznej bazy danych lub ustaw **skipCertificateChainValidation** właściwości **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Dlaczego uzyskać wystąpił błąd uwierzytelniania podczas klonowania repozytorium Git?
Jeśli używasz Menedżera poświadczeń Git lub jeśli próbujesz Klonuj repozytorium Git, za pomocą programu Visual Studio, możesz napotkać znany problem z okna dialogowego poświadczeń systemu Windows. Okno dialogowe ogranicza długość hasła do 127 znaków i go obcina hasło wygenerowane firmy Microsoft. Pracujemy nad skrócenie hasło. Na razie użyj Git Bash sklonować repozytorium Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Zarządzanie interfejsami API działa z Azure ExpressRoute?
Tak. Zarządzanie interfejsami API działa usługa Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Dlaczego, po wdrożeniu usługi API Management do nich, jest wymagamy dedykowanych podsieci w stylu Menedżera zasobów sieci wirtualnych?
Konieczność dedykowanych podsieci interfejsu API zarządzania pochodzi z faktu, który jest zbudowany na (Warstwa 1 PAAS) klasycznego modelu wdrożenia. Możemy wdrożyć w sieci Wirtualnej Menedżera zasobów (Warstwa 2), są konsekwencje z kodem. Klasycznego modelu wdrożenia na platformie Azure nie jest ściśle powiązane z modelu Resource Manager i dlatego w przypadku utworzenia zasobu w warstwie V2, warstwy V1 nie wiesz i problemów może się zdarzyć, takich jak zarządzanie interfejsami API próby wykorzystania adresu IP, który został już przydzielony do karty Sieciowej (oparta na V2).
Aby dowiedzieć się więcej na temat różnica modeli Classic i Menedżera zasobów Azure dotyczą [różnica w modelach wdrażania](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Co to jest rozmiar podsieci minimalne podczas wdrażania interfejsu API zarządzania w sieci Wirtualnej?
Rozmiar podsieci minimalne wymagane do wdrożenia usługi API Management jest [/29](../virtual-network/virtual-networks-faq.md#configuration), czyli minimalna podsieci o rozmiarze obsługującego Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Można przenieść usługi API Management z jedną subskrypcją do innego?
Tak. Aby dowiedzieć się więcej, zobacz temat [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem Moje interfejsu API?
[Znane problemy i ograniczenia](api-management-api-import-restrictions.md) dla Otwórz API(Swagger) WSDL i WADL formaty.
