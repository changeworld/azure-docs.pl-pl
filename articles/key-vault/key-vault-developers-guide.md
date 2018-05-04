---
title: Przewodnik dewelopera usługi Azure Key Vault
description: Deweloperzy mogą używać usługi Azure Key Vault, aby zarządzać kluczy kryptograficznych w środowisku Microsoft Azure.
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: alleonar
ms.openlocfilehash: 35e84a22eabce0b7361c2227317baab62421c301
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-key-vault-developers-guide"></a>Przewodnik dewelopera usługi Azure Key Vault

Magazyn kluczy umożliwia bezpieczny dostęp do poufnych informacji w aplikacjach:

- Klucze i klucze tajne, które są chronione bez konieczności pisania kodu, samodzielnie i łatwo można z nich korzystać z aplikacji.
- Mogą mieć własne klientów i zarządzanie własne klucze, więc użytkownik może skupić się na tworzeniu podstawowych funkcji oprogramowania. W ten sposób aplikacje nie będą właścicielami odpowiedzialności ani zobowiązań potencjalnych klientów dzierżawy kluczy i kluczy tajnych.
- Aplikacja może używać klucze do podpisywania i szyfrowania jeszcze utrzymują zarządzania kluczami zewnętrznych z aplikacji, umożliwiając rozwiązanie, aby umożliwiać jako aplikację geograficznie rozproszonych.
- Począwszy od września 2016 wersji usługi Key Vault, aplikacje można teraz używać usługi Key Vault [certyfikaty](https://docs.microsoft.com/rest/api/keyvault/certificate-operations). Aby uzyskać więcej informacji, zobacz [o kluczy, kluczy tajnych i certyfikaty](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Aby uzyskać więcej ogólnych informacji dotyczących usługi Azure Key Vault, zobacz [co to jest magazyn kluczy](key-vault-whatis.md).

## <a name="public-previews"></a>Podglądy publiczny

Okresowo wydania publicznej wersji zapoznawczej nowych funkcji usługi Key Vault. Wypróbuj te i Daj nam znać, co myślisz za pośrednictwem azurekeyvault@microsoft.com, adres e-mail naszych opinii.

### <a name="storage-account-keys---july-10-2017"></a>Klucze konta magazynu - 10 lipca 2017 r.

>[!NOTE]
>Dla tej aktualizacji z usługi Azure Key Vault tylko **klucze konta magazynu** jest funkcja w wersji zapoznawczej.

Ta wersja zapoznawcza zawiera naszej nowej klucze konta magazynu funkcji dostępnych za pośrednictwem tych interfejsów; [.NET / C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) i [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Aby uzyskać więcej informacji na temat nowych funkcji klucze konta magazynu, zobacz [omówienie klucze konta magazynu usługi Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Filmy wideo

To wideo pokazuje, jak utworzyć magazynu kluczy i jak z niego korzystać z aplikacji przykładowej "Hello Key Vault".

- [Magazyn kluczy developer — Przewodnik Szybki start](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Zasoby w powyższym wideo:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Usługi Azure Key Vault przykładowy kod](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Tworzenie i zarządzanie nimi magazynów kluczy

Usługa Azure Key Vault zapewnia bezpieczne przechowywanie poświadczeń i innych kluczy i kluczy tajnych, ale kodu musi zostać uwierzytelniona Key Vault w celu ich pobrania. Tożsamość usługi zarządzanej (MSI) powoduje, że rozwiązania tego problemu prostszy, zapewniając usług Azure automatycznie zarządzane tożsamości w usłudze Azure Active Directory (Azure AD). Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, w tym usługi Key Vault bez żadnych poświadczeń w kodzie. 

Aby uzyskać więcej informacji dotyczących MSI, zobacz [zarządzane usługi tożsamości (MSI) dla zasobów Azure](https://docs.microsoft.com/azure/active-directory/msi-overview).

Aby uzyskać więcej informacji na temat pracy z usługi AAD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](/active-directory/develop/active-directory-integrating-applications).

Przed rozpoczęciem pracy z kluczy i kluczy tajnych certyfikaty w magazynie kluczy, możesz utworzyć i zarządzać magazynu kluczy za pomocą interfejsu wiersza polecenia programu PowerShell, szablony Menedżera zasobów albo REST, zgodnie z opisem w następujących artykułach:

- [Tworzenie i Zarządzanie magazynami klucza z interfejsu wiersza polecenia](key-vault-manage-with-cli2.md)
- [Tworzenie i Zarządzanie magazynami klucza przy użyciu programu PowerShell](key-vault-get-started.md)
- [Utwórz magazyn kluczy i Dodaj klucz tajny za pomocą szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Tworzenie i Zarządzanie magazynami klucza z POZOSTAŁĄ](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kodowanie z magazynu kluczy

Magazyn kluczy systemu zarządzania dla programistów składa się z kilku interfejsów. Ta sekcja zawiera linki do wszystkich języków, a także niektóre exampls kodu. 

### <a name="supported-programming-and-scripting-languages"></a>Obsługiwanych językach programowania i skryptów

#### <a name="rest"></a>REST

Wszystkie zasoby magazynu kluczy są dostępne za pośrednictwem interfejsu REST; magazynów kluczy, kluczy tajnych, itp. 

[Dokumentacja interfejsu API REST magazynu kluczy](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[Interfejs API .NET porządkowy dla usługi Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Aby uzyskać więcej informacji w wersji 2.x zestawu .NET SDK, zobacz [informacje o wersji](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK dla magazynu kluczy](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

W środowisku Node.js interfejs API zarządzania magazyn kluczy i obiekt magazynu kluczy interfejsu API są niezależne. W następującym artykule omówienie daje dostęp do obu. 

[Azure Key Vault modułów dla środowiska Node.js](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Bibliotek platformy Azure Key Vault dla języka Python](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI dla magazynu kluczy](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Program Azure PowerShell dla magazynu kluczy](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Przewodniki Szybki start

- [Utwórz magazyn kluczy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Wprowadzenie do korzystania z usługi Key Vault w środowisku Node.js](https://azure.microsoft.com/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Przykłady kodu

Przykłady pełną za pomocą usługi Key Vault z aplikacji można znaleźć:

- [Przykłady kodu w usłudze Azure Key Vault](http://www.microsoft.com/download/details.aspx?id=45343) -.NET przykładowej aplikacji *HelloKeyVault* i przykład usługi sieci web platformy Azure. 
- [Użyj usługi Azure Key Vault z aplikacji sieci Web](key-vault-use-from-web-application.md) -samouczka, aby dowiedzieć się, jak używać usługi Azure Key Vault z aplikacji sieci web na platformie Azure. 

## <a name="how-tos"></a>Poradniki

Następujące artykuły i scenariusze zawierają wskazówki dotyczące zadań do pracy z usługą Azure Key Vault:

- [Przenieś zmiany Identyfikatora dzierżawcy magazynu kluczy po subskrypcji](key-vault-subscription-move-fix.md) — po przeniesieniu subskrypcji platformy Azure z dzierżawy A dzierżawcy B, Twoje istniejące magazynów kluczy są niedostępne podmiotów zabezpieczeń (Użytkownicy i aplikacje) w dzierżawie poprawka B. używania tego przewodnika.
- [Uzyskiwanie dostępu do usługi Key Vault za zaporą](key-vault-access-behind-firewall.md) — Aby uzyskać dostępu do aplikacji klienckiej magazynu kluczy musi być wiele punkty końcowe dla różnych funkcji dostęp do magazynu kluczy.
- [Jak Generowanie i Transfer HSM-Protected kluczy dla usługi Azure Key Vault](key-vault-hsm-protected-keys.md) — dzięki temu można zaplanować, generowanie i następnie przenieść własne klucze chronione przez moduł HSM do użycia z usługą Azure Key Vault.
- [Sposób przekazywania bezpiecznego wartości (na przykład hasła) podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md) — gdy należy przekazać wartość bezpiecznego (na przykład hasło) jako parametr podczas wdrażania, można przechowywać tej wartości jako klucza tajnego w magazynie kluczy Azure i odwołują się do wartości w innych zasobów Szablony menedżera.
- [Jak używać usługi Key Vault rozszerzonego zarządzania kluczami z programem SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -Łącznik usług SQL Server dla usługi Azure Key Vault umożliwia programu SQL Server i SQL w VM korzystać z usługi Azure Key Vault funkcję dostawcy rozszerzonego zarządzania klucza (EKM) do ochrony jego klucze szyfrowania dla łącza aplikacji; Przezroczystego szyfrowania danych, szyfrowania kopii zapasowych i szyfrowanie na poziomie kolumny.
- [Wdrażanie certyfikatów do maszyn wirtualnych z magazynu kluczy](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) — aplikacji w chmurze uruchomione na maszynie wirtualnej na potrzeby Azure certyfikatu. Jak uzyskać ten certyfikat do tej maszyny Wirtualnej dzisiaj?
- [Jak skonfigurować magazyn kluczy z rotacją kluczy pełnego i inspekcji](key-vault-key-rotation-log-monitoring.md) — to przeprowadzi Cię przez sposobu konfigurowania rotacją kluczy i inspekcji w usłudze Azure Key Vault.
- [Wdrażanie certyfikatów aplikacji sieci Web Azure za pomocą usługi Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) zawiera instrukcje krok po kroku dotyczące wdrażania certyfikaty przechowywane w magazynie kluczy jako część [certyfikatu usługi aplikacji](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) oferty.
- [Przyznanie uprawnień do wielu aplikacjom dostęp do magazynu kluczy](key-vault-group-permissions-for-apps.md) zasady kontroli dostępu do magazynu kluczy obsługuje tylko wpisy 16. Można jednak utworzyć grupy zabezpieczeń usługi Azure Active Directory. Dodaj do tej grupy zabezpieczeń wszystkich podmiotów zabezpieczeń skojarzona usługa, a następnie przyznać dostęp do tej grupy zabezpieczeń do magazynu kluczy.
- Dla poszczególnych zadań więcej pomocy na temat integracji i przy użyciu magazynów kluczy w usłudze Azure, zobacz [przykłady szablonów usługi Azure Resource Manager Nowak Ryan dla usługi Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Jak używać usługi Key Vault soft usunięcie z interfejsu wiersza polecenia](key-vault-soft-delete-cli.md) prowadzi użytkownika przez użycie i cyklem życia magazyn kluczy i różnych obiektów magazynu kluczy o usuwania nietrwałego, włączone.
- [Jak używać usługi Key Vault soft usunięcie przy użyciu programu PowerShell](key-vault-soft-delete-powershell.md) prowadzi użytkownika przez użycie i cyklem życia magazyn kluczy i różnych obiektów magazynu kluczy o usuwania nietrwałego, włączone.

## <a name="integrated-with-key-vault"></a>Zintegrowana z magazynu kluczy

Artykuły te są o innych scenariuszy i użyj lub zintegrować z magazynu kluczy usług.

- [Szyfrowanie dysków Azure](../security/azure-security-disk-encryption.md) korzysta ze standardu branżowego [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcji systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowany z usługą Azure Key Vault ułatwia kontrolowanie i zarządzanie nimi klucze szyfrowania dysku i kluczy tajnych w ramach subskrypcji magazynu kluczy, zapewniając, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane, gdy w magazynie Azure.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) udostępniana opcja szyfrowania danych przechowywanych w ramach konta. Zarządzania kluczami Data Lake Store zapewnia dwa tryby zarządzania kluczy szyfrowania głównego (MEKs), które są wymagane do odszyfrowywania danych przechowywanych w usłudze Data Lake Store. Można albo programowi Data Lake Store Zarządzanie MEKs lub zachować prawa własności MEKs przy użyciu konta usługi Azure Key Vault. Tryb zarządzania kluczami należy określić podczas tworzenia konta usługi Data Lake Store. 
- [Usługa Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) umożliwia Menedżera klucza dzierżawy. Na przykład firma Microsoft zarządza kluczem dzierżawy (opcja domyślna), można zarządzać własnym kluczem dzierżawy w celu zachowania zgodności z konkretnymi przepisami mającymi zastosowanie w danej organizacji. Zarządzanie własnym kluczem dzierżawy jest także określane jako Przynieś własny klucz lub BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Omówienie magazynu kluczy i pojęć

- [Zachowanie usuwania nietrwałego Key Vault](key-vault-ovw-soft-delete.md) opisuje funkcja, która umożliwia odzyskiwanie usuniętych obiektów, czy przypadkowym lub zamierzone został usunięty.
- [Ograniczanie klienta usługi Key Vault](key-vault-ovw-throttling.md) kieruje użytkowników do podstawowych pojęć dotyczących ograniczenia przepustowości i oferuje podejście do aplikacji.
- [Omówienie klucze konta magazynu usługi Key Vault](key-vault-ovw-storage-keys.md) opisano klucze konta magazynu Azure integracji magazynu kluczy.
- [Środowiska security World usługi Key Vault](key-vault-ovw-security-worlds.md) opisano relacje między regionami i obszary zabezpieczeń.

## <a name="social"></a>Społeczności

- [Blog magazyn kluczy](http://aka.ms/kvblog)
- [Forum magazyn kluczy](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Obsługa biblioteki

- [Biblioteka podstawowa magazynu Microsoft Azure klucza](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) zapewnia **IKey** i **IKeyResolver** interfejsy do lokalizowania kluczy z identyfikatorów i wykonywanie operacji z kluczami.
- [Rozszerzeń platformy Microsoft Azure Key Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) zapewnia rozszerzone możliwości usługi Azure Key Vault.


