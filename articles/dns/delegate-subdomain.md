---
title: Delegowanie poddomeny — usługa Azure DNS
description: Dzięki tej ścieżce szkoleniowej rozpocznij delegowanie poddomeny usługi Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: cd1443a9ca8ccf7172072078734f21d789d36194
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937429"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegowanie poddomeny usługi Azure DNS

Za pomocą portalu Azure można delegować poddomenę DNS. Jeśli na przykład jesteś właścicielem domeny contoso.com, możesz delegować poddomenę o nazwie *engineering* do innej oddzielnej strefy, którą można administrować oddzielnie od strefy contoso.com.

Jeśli wolisz, możesz delegować poddomenę za pomocą [programu Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby delegować poddomenę usługi Azure DNS, należy najpierw delegować domenę publiczną do usługi Azure DNS. Zobacz [delegowanie domeny do usługi Azure DNS, aby](./dns-delegate-domain-azure-dns.md) uzyskać instrukcje dotyczące konfigurowania serwerów nazw do delegowania. Po przekazaniu domeny do strefy DNS platformy Azure można delegować poddomenę.

> [!NOTE]
> Contoso.com jest używany jako przykład w tym artykule. Zastąp contoso.com swoją nazwą domeny.

## <a name="create-a-zone-for-your-subdomain"></a>Tworzenie strefy dla poddomeny

Najpierw utwórz strefę dla poddomeny **inżynierskiej.**

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób**.
2. W polu wyszukiwania **wpisz**DNS i wybierz **strefę DNS**.
3. Wybierz **pozycję Utwórz**.
4. W okienku **Tworzenie strefy DNS** wpisz **engineering.contoso.com** w polu tekstowym **Nazwa.**
5. Wybierz grupę zasobów dla swojej strefy. Można użyć tej samej grupy zasobów co strefa nadrzędna, aby zachować podobne zasoby razem.
6. Kliknij przycisk **Utwórz**.
7. Po pomyślnym wdrożeniu przejdź do nowej strefy.

## <a name="note-the-name-servers"></a>Zanotuj serwery nazw

Następnie zanotuj cztery serwery nazw poddomeny inżynierii.

W okienku strefy **inżynierskiej** zanotuj cztery serwery nazw strefy. Te serwery nazw będą używane później.

## <a name="create-a-test-record"></a>Tworzenie rekordu testu

Utwórz rekord **A** do użycia do testowania. Na przykład utwórz rekord **www** A i skonfiguruj go za pomocą adresu IP **10.10.10.10.**

## <a name="create-an-ns-record"></a>Tworzenie rekordu NS

Następnie utwórz rekord serwera nazw (NS) dla strefy **inżyniera.**

1. Przejdź do strefy dla domeny nadrzędnej.
2. Wybierz pozycję **+ Zestaw rekordów**.
3. W okienku **Dodawanie zestawu rekordów** wpisz **inżynierię** w polu tekstowym **Nazwa.**
4. W przypadku **opcji Typ**wybierz pozycję **NS**.
5. W obszarze **Serwer nazw**wprowadź cztery serwery nazw, które zostały wcześniej zarejestrowane ze strefy **inżynierskiej.**
6. Kliknij przycisk **OK**.

## <a name="test-the-delegation"></a>Testowanie delegacji

Użyj nslookup, aby przetestować delegowanie.

1. Otwórz okno programu PowerShell.
2. W wierszu polecenia wpisz`nslookup www.engineering.contoso.com.`
3. Należy otrzymać nieuzyskajną odpowiedź pod adresem **10.10.10.10**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować odwrotny system DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).