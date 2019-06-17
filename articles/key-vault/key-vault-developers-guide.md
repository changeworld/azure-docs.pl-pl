---
title: Przewodnik dewelopera usługi Azure Key Vault
description: Deweloperzy mogą używać usługi Azure Key Vault do zarządzania kluczami kryptograficznymi w środowisku Microsoft Azure.
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ec3080658b98376952f72f746c1b53fdf7de77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704341"
---
# <a name="azure-key-vault-developers-guide"></a>Przewodnik dewelopera usługi Azure Key Vault

Usługa Key Vault umożliwia bezpieczny dostęp do poufnych informacji z poziomu aplikacji:

- Klucze i wpisy tajne, które są chronione bez konieczności pisania kodu, samodzielnie i łatwo można z nich korzystać z aplikacji.
- Mogą mieć własne klientów i zarządzanie nimi własne klucze, dzięki czemu możesz skoncentrować się na dostarczaniu podstawowych funkcji oprogramowania. W ten sposób aplikacje nie będą właścicielami ponosić odpowiedzialności ani mieć potencjalnych problemów względem klientów dzierżawy kluczy i wpisów tajnych.
- Aplikacja może używać kluczy do podpisywania i szyfrowania jeszcze bardziej zarządzania kluczami zewnętrznych z aplikacji, dzięki czemu Twoje rozwiązanie jest odpowiednie, jako aplikacja rozproszona geograficznie.
- Począwszy od września 2016 r. wersję usługi Key Vault aplikacji mogą teraz zarządzać certyfikatami usługi Key Vault. Aby uzyskać więcej informacji, zobacz [o kluczy, wpisów tajnych i certyfikatów](/rest/api/keyvault/about-keys--secrets-and-certificates).

Aby uzyskać bardziej ogólne informacje na temat usługi Azure Key Vault, zobacz [co to jest usługa Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Okresowo wydaniu publiczną wersję zapoznawczą nowej funkcji usługi Key Vault. Wypróbuj je i Daj nam znać, co myślisz, za pośrednictwem azurekeyvault@microsoft.com, adres e-mail naszych opinii.

### <a name="storage-account-keys---july-10-2017"></a>Klucze konta magazynu — 10 lipca 2017 r.

>[!NOTE]
>Dla tej aktualizacji usługi Azure Key Vault tylko **kluczy konta magazynu** funkcja jest dostępna w wersji zapoznawczej.

Ta wersja zapoznawcza zawiera naszej nowej kluczy konta magazynu funkcji, dostępne za pośrednictwem tych interfejsów; [.NET / C#](/dotnet/api/microsoft.azure.keyvault/), [REST](/rest/api/keyvault/) i [PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault). 

Aby uzyskać więcej informacji na temat nowych funkcji kluczy konta magazynu, zobacz [omówienie kluczy konta magazynu usługi Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Filmy wideo

Ten film pokazuje, jak utworzyć magazyn kluczy i jak z niej korzystać z przykładowej aplikacji "Hello usługi Key Vault".

- [Deweloper usługi Key Vault — Przewodnik Szybki start](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Zasoby w powyższym wideo:

- [Azure PowerShell](https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Usługa Azure Key Vault przykładowy kod](https://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Tworzenie i Zarządzanie magazynami kluczy

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Zarządzanych tożsamości dla zasobów platformy Azure sprawia, że rozwiązywania tego problemu, prostsze, zapewniając tożsamości automatycznie zarządzanych usług platformy Azure w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie. 

Aby uzyskać więcej informacji na temat zarządzanych tożsamości dla zasobów platformy Azure, zobacz [Przegląd zarządzanych tożsamości](../active-directory/managed-identities-azure-resources/overview.md). Aby uzyskać więcej informacji na temat pracy z usługą AAD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Przed rozpoczęciem pracy z kluczy, wpisów tajnych lub certyfikatów w magazynie kluczy, możesz utworzyć i zarządzania magazynem kluczy przy użyciu interfejsu wiersza polecenia, programu PowerShell, szablony usługi Resource Manager lub REST, zgodnie z opisem w następujących artykułach:

- [Tworzenie i zarządzanie nimi Key Vault z interfejsem wiersza polecenia](key-vault-manage-with-cli2.md)
- [Tworzenie i zarządzanie nimi Key Vault przy użyciu programu PowerShell](key-vault-overview.md)
- [Tworzenie magazynu kluczy i dodać wpis tajny przy użyciu szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Tworzenie i zarządzanie nimi Key Vault za pomocą architektury REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kodowanie za pomocą usługi Key Vault

System zarządzania usługi Key Vault dla programistów składa się z kilku interfejsów. Ta sekcja zawiera linki do wszystkich języków, a także kilka przykładów kodu. 

### <a name="supported-programming-and-scripting-languages"></a>Obsługiwanych językach programowania i skryptów

#### <a name="rest"></a>REST

Wszystkie zasoby usługi Key Vault są dostępne za pośrednictwem interfejsu REST; magazyny kluczy, wpisów tajnych, itp. 

[Dokumentacja interfejsu API REST magazynu kluczy](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Dokumentacja interfejsu API .NET usługi Key Vault](/dotnet/api/microsoft.azure.keyvault).

Aby uzyskać więcej informacji na temat zestawu SDK programu .NET w wersji 2.x, zobacz [informacje o wersji](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK dla usługi Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

W języku Node.js interfejs API zarządzania usługi Key Vault i interfejsu API obiektu usługi Key Vault są oddzielone. Poniższy artykuł z omówieniem daje dostęp do obu. 

[Moduły platformy Azure Key Vault dla środowiska Node.js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Biblioteki usługi Azure Key Vault dla języka Python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Interfejs wiersza polecenia platformy Azure 2

[Wiersza polecenia platformy Azure dla usługi Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Program Azure PowerShell dla usługi Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quick-start-guides"></a>Przewodniki Szybki start

- [Tworzenie magazynu kluczy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Wprowadzenie do usługi Key Vault w środowisku Node.js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Przykłady kodu

Aby uzyskać kompletny przykład za pomocą usługi Key Vault z aplikacjami Zobacz:

- [Przykłady kodu usługi Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) — przykłady kodu usługi Azure Key Vault. 
- [Użyj usługi Azure Key Vault z aplikacji sieci Web](quick-create-net.md) -samouczka, aby dowiedzieć się, jak używać usługi Azure Key Vault z aplikacji sieci web na platformie Azure. 

## <a name="how-tos"></a>Poradniki

Następujące artykuły i scenariusze zawierają ze wskazówek specyficznych dla zadań do pracy z usługą Azure Key Vault:

- [Zmiana Identyfikatora dzierżawy magazynu kluczy po subskrypcji przenieść](key-vault-subscription-move-fix.md) — po przeniesieniu subskrypcji platformy Azure z dzierżawy A do dzierżawy B istniejące magazyny kluczy są niedostępne dla nazw głównych (użytkowników i aplikacji) w dzierżawie B. poprawkę tego za pomocą tego przewodnika.
- [Uzyskiwanie dostępu do usługi Key Vault za zaporą](key-vault-access-behind-firewall.md) — do dostępu do magazynu kluczy, aplikacja kliencka magazynu kluczy musi mieć możliwość dostępu do wielu punktów końcowych dla różnych funkcji.
- [Generowanie i Transfer HSM-Protected kluczy dla usługi Azure Key Vault](key-vault-hsm-protected-keys.md) -ułatwi to planowanie, generowanie i następnie przenoszenie własnych kluczy chronionych modułem HSM za pomocą usługi Azure Key Vault.
- [Przekazywanie bezpiecznych wartości (na przykład hasła) podczas wdrażania jak](../azure-resource-manager/resource-manager-keyvault-parameter.md) — w przypadku należy przekazać wartość bezpieczną (na przykład hasło) jako parametr podczas wdrażania, można przechowywać wartości jako klucz tajny w usłudze Azure Key Vault i odwoływać się do wartości w innych zasobów Menedżer szablonów.
- [Jak używać usługi Key Vault dla rozszerzonego zarządzania kluczami z programem SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) — Łącznik usług SQL Server dla usługi Azure Key Vault umożliwia programu SQL Server i SQL w VM korzystać z usługi Azure Key Vault jako dostawcy rozszerzonego zarządzania kluczami (EKM) do ochrony jego klucze szyfrowania na potrzeby łącze do aplikacji; Transparent Data Encryption, szyfrowania kopii zapasowych i szyfrowania na poziomie kolumny.
- [Jak wdrożyć certyfikaty na maszynach wirtualnych z usługi Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) — uruchomiona na maszynie wirtualnej platformy Azure wymagań dotyczących certyfikatu aplikacji w chmurze. Jak można uzyskać ten certyfikat do tej maszyny Wirtualnej już dzisiaj?
- [Jak skonfigurować usługi Key Vault przy użyciu typu end to end rotacji i inspekcji kluczy](key-vault-key-rotation-log-monitoring.md) — to zawiera szczegółowe instrukcje dotyczące konfigurowania rotacji kluczy i przeprowadzanie inspekcji za pomocą usługi Azure Key Vault.
- [Wdrażanie certyfikatu aplikacji sieci Web platformy Azure za pośrednictwem usługi Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) zawiera instrukcje krok po kroku dotyczące wdrażania certyfikatów przechowywanych w usłudze Key Vault w ramach [certyfikatu usługi App Service](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) oferty.
- [Przyznaj uprawnienie do wielu aplikacji do dostępu do magazynu kluczy](key-vault-group-permissions-for-apps.md) zasady kontroli dostępu do usługi Key Vault obsługuje maksymalnie 1024 wpisów. Można jednak utworzyć grupę zabezpieczeń usługi Azure Active Directory. Dodaj wszystkie jednostki usługi skojarzonego do tej grupy zabezpieczeń, a następnie przyznać dostęp do tej grupy zabezpieczeń w usłudze Key Vault.
- Aby uzyskać więcej wskazówek specyficznych dla zadań na integrowanie i używania magazynów kluczy na platformie Azure, zobacz [Ryan Jones przykłady szablonów usługi Azure Resource Manager dla usługi Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](key-vault-soft-delete-cli.md) przejdziesz przez użycie i cykl życia magazynu kluczy i różnych obiektów magazynu kluczy przy użyciu opcji soft-delete włączone.
- [Jak używać usuwania nietrwałego w usłudze Key Vault przy użyciu programu PowerShell](key-vault-soft-delete-powershell.md) przejdziesz przez użycie i cykl życia magazynu kluczy i różnych obiektów magazynu kluczy przy użyciu opcji soft-delete włączone.

## <a name="integrated-with-key-vault"></a>Zintegrowana z usługą Key Vault

Te artykuły są dotyczących innych scenariuszy lub usług, które używane lub zintegrować z usługą Key Vault.

- [Usługa Azure Disk Encryption](../security/azure-security-disk-encryption.md) wykorzystuje będące standardami branżowymi [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcja systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowana z usługą Azure Key Vault ułatwia kontrolowanie oraz zarządzanie tymi wpisami tajnymi w ramach subskrypcji usługi key vault, przy jednoczesnym zapewnieniu, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage i kluczami szyfrowania dysków.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) udostępniana opcja szyfrowania danych przechowywanych na koncie. Do zarządzania kluczami Data Lake Store udostępnia dwa tryby zarządzania kluczami szyfrowania głównego (głównymi kluczami szyfrowania), które są wymagane w celu odszyfrowania żadnych danych, która jest przechowywana w Data Lake Store. Można albo pozwolić Data Lake Store zarządzania głównymi kluczami szyfrowania lub zachowanych własności głównymi kluczami szyfrowania za pomocą konta usługi Azure Key Vault. Tryb zarządzania kluczami należy określić podczas tworzenia konta Data Lake Store.
- [Usługa Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) pozwala na Menedżera własnym kluczem dzierżawy. Na przykład firma Microsoft zarządza kluczem dzierżawy (opcja domyślna), można zarządzać własnym kluczem dzierżawy w celu zachowania zgodności z konkretnymi przepisami mającymi zastosowanie w danej organizacji. Zarządzanie własnym kluczem dzierżawy jest również określany jako bring własnego klucza — byok.

## <a name="key-vault-overviews-and-concepts"></a>Omówienie usługi Key Vault i pojęcia

- [Zachowanie usuwania nietrwałego w usłudze Key Vault](key-vault-ovw-soft-delete.md) opisuje funkcja, która umożliwia odzyskiwanie usuniętych obiektów, czy został usunięty, przypadkowym lub zamierzone.
- [Klient usługi Key Vault ograniczania](key-vault-ovw-throttling.md) kieruje użytkowników do podstawowych pojęć dotyczących ograniczenia przepustowości i oferuje podejście dla aplikacji.
- [Omówienie kluczy konta magazynu Key Vault](key-vault-ovw-storage-keys.md) opisano klucze kont usługi Azure Storage integracji usługi Key Vault.
- [Środowiska zabezpieczeń usługi Key Vault](key-vault-ovw-security-worlds.md) opisano relacje między regionami i obszary zabezpieczeń.

## <a name="social"></a>Społeczności

- [Blog usługi Key Vault](https://aka.ms/kvblog)
- [Forum usługi Key Vault](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Obsługa bibliotek

- [Microsoft Azure klucza magazynu podstawowej biblioteki](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) zapewnia **IKey** i **IKeyResolver** interfejsy do lokalizowania kluczy z identyfikatorów i wykonywanie operacji za pomocą kluczy.
- [Rozszerzeń platformy Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) zapewnia rozszerzone możliwości usługi Azure Key Vault.
