---
title: Cloud Services i zarządzanie certyfikatami | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i używać certyfikatów z platformą Microsoft Azure
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 4ca26c7b8fbfebbce8cfcb9915a7db12e5ad2352
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337412"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Omówienie certyfikatów usług Azure Cloud Services
Certyfikaty są używane na platformie Azure dla usług w chmurze ([usługi certyfikatów](#what-are-service-certificates)) i uwierzytelniania za pomocą interfejsu API zarządzania ([certyfikaty zarządzania](#what-are-management-certificates)). Ten temat zawiera ogólne omówienie oba typy certyfikatów, jak do [tworzenie](#create) i wdrażać je na platformie Azure.

Certyfikaty używane na platformie Azure są x.509 v3 certyfikaty i może być podpisany przez inny zaufanego certyfikatu, lub może być podpisem. Certyfikat z podpisem własnym jest podpisany przez własny twórcy, w związku z tym nie jest zaufany przez domyślny. W większości przeglądarek, można zignorować ten problem. Należy używać tylko certyfikatów z podpisem własnym podczas tworzenia i testowania usługi w chmurze. 

Certyfikaty używane przez platformę Azure może zawierać prywatnej lub klucza publicznego. Certyfikaty muszą odcisku palca, zapewniająca oznacza, że można je zidentyfikować w jednoznaczny sposób. To odcisk palca jest używany na platformie Azure [pliku konfiguracji](cloud-services-configure-ssl-certificate-portal.md) do identyfikowania należy używać certyfikatu której usługa w chmurze. 

>[!Note]
>Usługi Azure Cloud Services nie akceptuje AES256 SHA256 zaszyfrowany certyfikat.

## <a name="what-are-service-certificates"></a>Co to są usługi certyfikatów?
Certyfikaty usług są dołączone do usług w chmurze i Włącz bezpieczną komunikację do i z usługi. Na przykład jeśli wdrożono rolę sieci web, należy podać certyfikat, który może uwierzytelniać narażonych punktu końcowego HTTPS. Certyfikaty usługi, jest zdefiniowany w definicji usługi, są automatycznie wdrażane na maszynę wirtualną, która jest uruchomione wystąpienie roli użytkownika. 

Możesz przekazać certyfikaty usług na platformie Azure za pomocą witryny Azure portal lub za pomocą klasycznego modelu wdrażania. Certyfikaty usług są skojarzone z usługą w chmurze. Są one przypisane do wdrożenia w pliku definicji usługi.

Certyfikaty usługi może być zarządzany oddzielnie od usług i mogą być zarządzane przez inne osoby. Na przykład deweloper może przekazać pakiet usługi, która odwołuje się do certyfikatu, który jest Menedżer wcześniej został przekazany na platformę Azure. Menedżer IT, można zarządzać i odnawiania certyfikatu (zmienianie konfiguracji usługi) bez konieczności przekazywania nowego pakietu usługi. Aktualizowanie bez nowego pakietu usługi jest możliwe, ponieważ nazwa logiczna nazwa magazynu i lokalizację certyfikatu znajduje się w pliku definicji usługi, i gdy odcisk palca certyfikatu jest określona w pliku konfiguracji usługi. Aby zaktualizować certyfikat, jest tylko niezbędne do przekazania nowego certyfikatu, a następnie zmień wartość odcisku palca w pliku konfiguracji usługi.

>[!Note]
>[Usług w chmurze często zadawane pytania](cloud-services-faq.md) artykuł zawiera pewne przydatne informacje dotyczące certyfikatów.

## <a name="what-are-management-certificates"></a>Co to są certyfikaty zarządzania?
Certyfikaty zarządzania umożliwiają uwierzytelnianie przy użyciu klasycznego modelu wdrażania. Wiele programów i narzędzi (takich jak Visual Studio lub zestawu SDK platformy Azure) Automatyzuj Konfigurowanie i wdrażanie różnych usług platformy Azure przy użyciu funkcji tych certyfikatów. Te nie są naprawdę związane z usługami w chmurze. 

> [!WARNING]
> Ostrożnie! Zezwalaj na tych typów certyfikatów, każdy, kto uwierzytelnia się za pomocą ich do zarządzania subskrypcją, które są skojarzone. 
> 
> 

### <a name="limitations"></a>Ograniczenia
Obowiązuje limit 100 certyfikatów zarządzania na subskrypcję. Istnieje również limit 100 certyfikaty zarządzania dla wszystkich subskrypcji w ramach identyfikatora administratora usługi określonego użytkownika. Identyfikator użytkownika administratora konta został już użyty do dodania 100 certyfikaty zarządzania, jeśli ma potrzeby certyfikatów, można dodać administratora współpracującego, aby dodać dodatkowych certyfikatów. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Utwórz nowy certyfikat z podpisem własnym
Można użyć dowolnego narzędzia, które są dostępne utworzyć certyfikat z podpisem własnym, tak długo, jak stosować się do tych ustawień:

* Certyfikat X.509.
* Zawiera klucz prywatny.
* Utworzone na potrzeby wymiany kluczy (plik pfx).
* Nazwa podmiotu musi odpowiadać domena używana do uzyskania dostępu do usługi w chmurze.

    > Nie można uzyskać certyfikatu SSL dla cloudapp.net (ani dotyczących platformy Azure) domeny. Nazwa podmiotu certyfikatu musi odpowiadać niestandardowej nazwy domeny umożliwia dostęp do Twoich aplikacji. Na przykład **"contoso.NET"**, a nie **contoso.cloudapp.net**.

* Co najmniej szyfrowanie 2048-bitowe.
* **Usługi certyfikatów tylko**: Po stronie klienta, certyfikat musi znajdować się w *osobistych* magazynu certyfikatów.

Istnieją dwa sposoby łatwo można utworzyć certyfikatu na Windows, za pomocą `makecert.exe` narzędzia lub usługi IIS.

### <a name="makecertexe"></a>Makecert.exe
Narzędzie to jest przestarzała i nie jest już opisanych tutaj. Aby uzyskać więcej informacji, zobacz [w tym artykule MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Jeśli chcesz użyć certyfikatu z adresem IP, a nie do domeny, należy użyć adresu IP w parametrze - DnsName.


Jeśli chcesz użyć tej funkcji [certyfikatu za pomocą portalu zarządzania](../azure-api-management-certs.md), wyeksportować je do **cer** pliku:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Istnieje wiele stron w Internecie, które opisano, jak to zrobić za pomocą programu IIS. [W tym miejscu](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) to fantastyczne uczucie udało mi się znaleźć traktować wyjaśnia on również. 

### <a name="linux"></a>Linux
[To](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykule opisano sposób tworzenia certyfikatów przy użyciu protokołu SSH.

## <a name="next-steps"></a>Kolejne kroki
[Przekaż certyfikat usługi do witryny Azure portal](cloud-services-configure-ssl-certificate-portal.md).

Przekaż [certyfikatu interfejsu API zarządzania](../azure-api-management-certs.md) do witryny Azure portal.

