---
title: Skojarzenie równorzędnej subskrypcji ASN z usługą Azure przy użyciu programu PowerShell
titleSuffix: Azure
description: Skojarzenie równorzędnej subskrypcji ASN z usługą Azure przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908982"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Skojarzenie równorzędnej subskrypcji ASN z usługą Azure przy użyciu programu PowerShell

Przed przesłaniem żądania komunikacji równorzędnej należy najpierw skojarzyć asn z subskrypcją platformy Azure, wykonując poniższe kroki.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą [portalu](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Tworzenie usługi PeerASN w celu skojarzenia sieci ASN z subskrypcją platformy Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Zarejestruj się dla dostawcy zasobów komunikacji równorzędnej
Zarejestruj się dla dostawcy zasobów komunikacji równorzędnej w ramach subskrypcji, korzystając z poniższego polecenia. Jeśli nie wykonasz tego, zasoby platformy Azure wymagane do skonfigurowania komunikacji równorzędnej nie są dostępne.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Stan rejestracji można sprawdzić za pomocą poniższych poleceń:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Poczekaj, *aż registrationstate* włączyć "Zarejestrowany" przed kontynuowaniem. Może upłynąć od 5 do 30 minut po wykonaniu polecenia.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Aktualizowanie informacji elementów równorzędnych skojarzonych z tą subskrypcją

Poniżej znajduje się przykład aktualizacji informacji równorzędnych.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name odpowiada nazwie zasobu i może być wszystko, co wybierzesz. Jednak -peerName odpowiada nazwie firmy i musi być jak najbliżej profilu PeeringDB. Należy zauważyć, że wartość -peerName obsługuje tylko znaki a-z, A-Z i spacji.

Subskrypcja może mieć wiele sieci ASN. Zaktualizuj informacje dotyczące komunikacji równorzędnej dla każdego asn. Upewnij się, że "name" jest unikatowa dla każdego ASN.

Oczekuje się, że elementy równorzędne będą miały pełny i aktualny profil w [peeringdb](https://www.peeringdb.com). Używamy tych informacji podczas rejestracji, aby zweryfikować dane peera, takie jak informacje NOC, techniczne informacje kontaktowe i ich obecność w obiektach komunikacji równorzędnej itp.

Należy zauważyć, że zamiast **{subscriptionId}** w danych wyjściowych powyżej zostanie wyświetlony rzeczywisty identyfikator subskrypcji.

## <a name="view-status-of-a-peerasn"></a>Wyświetl stan peerasn

Sprawdź stan sprawdzania poprawności ASN za pomocą poniższego polecenia:

```powershell
Get-AzPeerAsn
```

Poniżej znajduje się przykładowa odpowiedź:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Poczekaj na validationstate, aby włączyć "Zatwierdzone" przed przesłaniem żądania komunikacji równorzędnej. Zatwierdzenie to może potrwać do 12 godzin.

## <a name="modify-peerasn"></a>Modyfikowanie elementu PeerAsn
Użytkownik może w każdej chwili zmodyfikować informacje kontaktowe NOC.

Poniżej znajduje się przykład:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Usuń PeerAsn
Usuwanie peerasn nie jest obecnie obsługiwane. Jeśli chcesz usunąć PeerASN, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej](howto-direct-powershell.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure](howto-legacy-direct-powershell.md)
* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)
