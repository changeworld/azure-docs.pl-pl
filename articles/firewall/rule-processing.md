---
title: Logika przetwarzania reguł usługi Azure Firewall
description: Zapora platformy Azure ma reguły NAT, reguły sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264780"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika przetwarzania reguł usługi Azure Firewall
Reguły translatora adresów sieciowych, reguły sieci i aplikacje można skonfigurować w zaporze platformy Azure. Reguły są przetwarzane zgodnie z typem reguły. 

> [!NOTE]
> W przypadku włączenia filtrowania opartego na analizie zagrożeń te reguły mają najwyższy priorytet i są zawsze przetwarzane jako pierwsze. Filtrowanie analizy zagrożeń może odmówić ruchu przed przetworzeniem skonfigurowanych reguł. Aby uzyskać więcej informacji, zobacz [filtrowanie oparte na analizie zagrożeń platformy Azure](threat-intel.md).

## <a name="outbound"></a>Wychodzący

### <a name="network-rules-and-applications-rules"></a>Reguły sieci i zasady dotyczące aplikacji

W przypadku konfigurowania reguł sieci i reguł aplikacji reguły sieci są stosowane w kolejności priorytetu przed regułami aplikacji. Reguły są przerywane. Dlatego w przypadku znalezienia dopasowania w regule sieciowej nie są przetwarzane żadne inne reguły.  Jeśli nie ma dopasowania reguły sieci i jeśli protokół to HTTP, HTTPS lub MSSQL, pakiet jest następnie oceniany przez reguły aplikacji w kolejności priorytetów. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet jest oceniany względem [kolekcji reguł infrastruktury](infrastructure-fqdns.md). Jeśli wciąż nie zostanie znalezione dopasowanie, pakiet zostanie domyślnie odrzucony.

## <a name="inbound"></a>Przychodzący

### <a name="nat-rules"></a>Reguły NAT

Przychodzące połączenie internetowe można włączyć przez skonfigurowanie translacji adresów sieciowych (DNAT), zgodnie z opisem w [samouczku: filtrowanie ruchu przychodzącego za pomocą zapory platformy Azure DNAT przy użyciu Azure Portal](tutorial-firewall-dnat.md). Reguły NAT są stosowane w priorytecie przed regułami sieci. Jeśli zostanie znalezione dopasowanie, niejawna odpowiadająca reguła sieci umożliwia dodanie przetłumaczonego ruchu. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem.

Reguły aplikacji nie są stosowane do połączeń przychodzących. Dlatego jeśli chcesz filtrować ruch przychodzący HTTP/S, należy użyć zapory aplikacji sieci Web (WAF). Aby uzyskać więcej informacji, zobacz [co to jest Zapora aplikacji sieci Web platformy Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Przykłady

Poniższe przykłady pokazują wyniki niektórych kombinacji reguł.

### <a name="example-1"></a>Przykład 1

Połączenie z usługą google.com jest dozwolone z powodu zgodnej reguły sieci.

**Reguła sieci**

- Akcja: Zezwalaj


|{1&gt;nazwa&lt;1}  |Protokół  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Zezwalaj — sieć Web     |TCP|Adres IP|*|Adres IP|*|80,443

**Reguła aplikacji**

- Akcja: Odmów

|{1&gt;nazwa&lt;1}  |Typ źródła  |Element źródłowy  |Protokół: Port|Docelowe nazwy FQDN|
|---------|---------|---------|---------|----------|----------|
|Odmów — Google     |Adres IP|*|http: 80, https: 443|google.com

**Wynika**

Połączenie z usługą google.com jest dozwolone, ponieważ pakiet jest zgodny z regułą *Zezwalaj-sieci Web* . Przetwarzanie reguły zostało zatrzymane w tym momencie.

### <a name="example-2"></a>Przykład 2

Odmowa ruchu SSH, ponieważ wyższy priorytet *odmówi* kolekcji reguł sieci.

**Kolekcja reguł sieciowych 1**

- Name: Allow-Collection
- Priorytet: 200
- Akcja: Zezwalaj

|{1&gt;nazwa&lt;1}  |Protokół  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Zezwalaj — SSH     |TCP|Adres IP|*|Adres IP|*|22

**Kolekcja reguł sieci 2**

- Nazwa: Odmów — zbieranie
- Priorytet: 100
- Akcja: Odmów

|{1&gt;nazwa&lt;1}  |Protokół  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Odmów — SSH     |TCP|Adres IP|*|Adres IP|*|22

**Wynika**

Odmowa połączeń SSH, ponieważ kolekcja reguł sieci o wyższym priorytecie jest blokowana. Przetwarzanie reguły zostało zatrzymane w tym momencie.

## <a name="rule-changes"></a>Zmiany reguły

Jeśli zmienisz regułę tak, aby odmówił wcześniej dozwolony ruch, wszelkie odpowiednie istniejące sesje zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).