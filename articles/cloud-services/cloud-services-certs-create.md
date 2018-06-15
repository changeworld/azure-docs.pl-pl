---
title: Usługi i certyfikaty zarządzania w chmurze | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i używania certyfikatów przy użyciu systemu Microsoft Azure
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 6a1e4f5316cc0321c1409f9e48daeae6ee483bf6
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29969206"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Omówienie certyfikatów dla usług w chmurze Azure
Certyfikaty są używane na platformie Azure do usługi w chmurze ([usługi certyfikatów](#what-are-service-certificates)) i do uwierzytelniania z interfejsem API zarządzania ([certyfikaty zarządzania](#what-are-management-certificates)). Ten temat zawiera ogólne omówienie obu typów certyfikatów, jak do [utworzyć](#create) i [wdrażanie](#deploy) je do platformy Azure.

Certyfikaty używane na platformie Azure są x.509 v3 certyfikatów i może być podpisany przez inny zaufanego certyfikatu lub można je podpisem. Certyfikatu z podpisem własnym jest podpisany przez jego własnej twórcy, w związku z tym nie jest zaufany domyślnie. W większości przeglądarek, można zignorować ten problem. Należy używać tylko certyfikaty z podpisem własnym podczas tworzenia i testowania usługi w chmurze. 

Certyfikaty używane przez usługę Azure może zawierać prywatnej lub klucza publicznego. Certyfikaty mają odcisku palca, które pozwala zidentyfikować je w sposób jednoznaczny. Odciskiem palca jest używany w Azure [pliku konfiguracyjnego](cloud-services-configure-ssl-certificate-portal.md) do identyfikowania należy używać który certyfikatów usługi w chmurze. 

## <a name="what-are-service-certificates"></a>Co to są certyfikaty usługi?
Usługi certyfikatów są dołączone do usługi w chmurze i komunikacja zabezpieczona do i z usługi. Na przykład jeśli wdrożono rolę sieci web, czy chcesz dostarczyć certyfikat, który może uwierzytelniać narażonych punkt końcowy HTTPS. Usługi certyfikatów, zdefiniowane w definicji usługi, są automatycznie wdrażane na maszynę wirtualną, która jest uruchomione wystąpienie roli użytkownika. 

Możesz przekazać certyfikaty usług Azure za pomocą portalu Azure lub przy użyciu klasycznego modelu wdrażania. Usługi certyfikatów skojarzonych z usługą w chmurze określonych. Są one przypisane do wdrożenia w pliku definicji usługi.

Usługi certyfikatów mogą być zarządzana oddzielnie od usługi i mogą być zarządzane przez różne osoby. Na przykład deweloper może przekazać pakietu usługi, który odwołuje się do certyfikatu, który jest Menedżer został wcześniej przekazany do platformy Azure. Menedżer można zarządzać i odnawianie certyfikatu (zmiana konfiguracji usługi) bez konieczności przekazać nowy pakiet usługi. Aktualizowanie bez nowego pakietu usługi jest możliwe, ponieważ nazwa logiczna nazwa magazynu i lokalizację certyfikatu znajduje się w pliku definicji usługi, a podczas odcisk palca certyfikatu jest określony w pliku konfiguracji usługi. Aby zaktualizować certyfikat, tylko jest to konieczne przekazać nowy certyfikat i zmień wartość odcisku palca w pliku konfiguracji usługi.

>[!Note]
>[Usługi w chmurze — często zadawane pytania](cloud-services-faq.md) artykuł zawiera niektóre przydatne informacje na temat certyfikatów.

## <a name="what-are-management-certificates"></a>Co to są certyfikaty zarządzania?
Certyfikaty zarządzania pozwalają do uwierzytelniania w klasycznym modelu wdrażania. Wiele programów i narzędzia (np. programu Visual Studio lub zestawu Azure SDK) korzystały z tych certyfikatów można zautomatyzować konfigurowanie i wdrażanie różnych usług platformy Azure. Te nie są naprawdę związane z usługami w chmurze. 

> [!WARNING]
> Ostrożnie. Te typy certyfikatów umożliwiają każdego, kto jest uwierzytelniany w usłudze ich do zarządzania subskrypcją, które są skojarzone. 
> 
> 

### <a name="limitations"></a>Ograniczenia
Istnieje limit 100 certyfikatów zarządzania dla subskrypcji. Istnieje również limit 100 certyfikatów zarządzania dla wszystkich subskrypcji w obszarze Nazwa użytkownika administratora określonej usługi. Jeśli istnieje potrzeba więcej certyfikatów można dodać 100 certyfikaty zarządzania został już użyty identyfikator użytkownika dla konta administratora, możesz dodać współadministrator można dodać dodatkowych certyfikatów. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Utwórz nowy certyfikat z podpisem własnym
Można użyć dowolnego narzędzia, które są dostępne do utworzenia certyfikatu z podpisem własnym, tak długo, jak stosować się do tych ustawień:

* Certyfikat X.509.
* Zawiera klucz prywatny.
* Utworzone dla wymiany kluczy (pfx).
* Nazwa podmiotu musi odpowiadać domeny używane do uzyskania dostępu do usługi w chmurze.

    > Nie można pobrać certyfikatu SSL dla cloudapp.net (lub jakichkolwiek związanych z platformy Azure) domeny; Nazwa podmiotu certyfikatu musi odpowiadać nazwie domeny niestandardowej, umożliwiające dostęp do aplikacji. Na przykład **contoso.net**, a nie **contoso.cloudapp.net**.

* Co najmniej 2048-bitowego szyfrowania.
* **Usługi certyfikatów tylko**: po stronie klienta, certyfikat musi znajdować się w *osobistych* magazynu certyfikatów.

Istnieją dwa sposoby łatwe, aby utworzyć certyfikat w systemie Windows, za pomocą `makecert.exe` narzędzia lub usług IIS.

### <a name="makecertexe"></a>Makecert.exe
Narzędzie to jest przestarzała i nie jest już opisanych tutaj. Aby uzyskać więcej informacji, zobacz [ten artykuł w witrynie MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Jeśli chcesz użyć certyfikatu z adresem IP, a nie do domeny, użyj adresu IP w parametrze - DnsName.


Jeśli chcesz użyć tej funkcji [certyfikatu za pomocą portalu zarządzania](../azure-api-management-certs.md), eksportowania ich do **.cer** pliku:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internetowe usługi informacyjne (IIS)
Istnieje wiele stron w Internecie, które opisano, jak to zrobić w programie IIS. [W tym miejscu](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) jest doskonałym udało mi się znaleźć traktować go wyjaśniono również. 

### <a name="linux"></a>Linux
[To](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykuł opisuje sposób tworzenia certyfikatów przy użyciu protokołu SSH.

## <a name="next-steps"></a>Kolejne kroki
[Przekaż certyfikat usługi do portalu Azure](cloud-services-configure-ssl-certificate-portal.md).

Przekaż [certyfikat interfejsu API zarządzania](../azure-api-management-certs.md) do portalu Azure.

