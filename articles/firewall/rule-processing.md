---
title: Logika przetwarzania reguł usługi Azure Firewall
description: Zapora azure ma reguły NAT, reguły sieciowe i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264780"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika przetwarzania reguł usługi Azure Firewall
Reguły translatora i translatora kontaktów z siecią, reguły sieci i reguły aplikacji można skonfigurować w zaporze platformy Azure. Reguły są przetwarzane zgodnie z typem reguły. 

> [!NOTE]
> Jeśli włączysz filtrowanie oparte na analizie zagrożeń, te reguły mają najwyższy priorytet i są zawsze przetwarzane jako pierwsze. Filtrowanie analizy zagrożeń może odmówić ruchu przed przetworzeniem skonfigurowanych reguł. Aby uzyskać więcej informacji, zobacz [filtrowanie oparte na analizie zagrożeń zapory platformy Azure](threat-intel.md).

## <a name="outbound"></a>Wychodzący

### <a name="network-rules-and-applications-rules"></a>Reguły sieci i reguły aplikacji

Jeśli skonfigurujesz reguły sieciowe i reguły aplikacji, reguły sieciowe są stosowane w kolejności priorytetów przed regułami aplikacji. Zasady wygasają. Jeśli więc dopasowanie zostanie znalezione w regule sieci, żadne inne reguły nie są przetwarzane.  Jeśli nie ma dopasowania reguły sieciowej i jeśli protokół jest HTTP, HTTPS lub MSSQL, pakiet jest następnie oceniany przez reguły aplikacji w kolejności priorytetu. Jeśli nadal nie zostanie znalezione żadne dopasowanie, pakiet jest oceniany względem [kolekcji reguł infrastruktury](infrastructure-fqdns.md). Jeśli wciąż nie zostanie znalezione dopasowanie, pakiet zostanie domyślnie odrzucony.

## <a name="inbound"></a>Przychodzący

### <a name="nat-rules"></a>Reguły translatora i sieci zuchwów

Przychodzącą łączność z Internetem można włączyć, konfigurując translację docelowego adresu sieciowego (DNAT) zgodnie z opisem w [samouczku: Filtrowanie ruchu przychodzącego za pomocą usługi Azure Firewall DNAT przy użyciu portalu Azure](tutorial-firewall-dnat.md). Reguły translatora i obrotu są stosowane w priorytecie przed regułami sieciowymi. Jeśli zostanie znalezione dopasowanie, zostanie dodana niejawna odpowiednia reguła sieciowa zezwalana na przetłumaczony ruch. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem.

Reguły aplikacji nie są stosowane dla połączeń przychodzących. Jeśli więc chcesz filtrować przychodzący ruch HTTP/S, należy użyć Zapory aplikacji sieci Web (WAF). Aby uzyskać więcej informacji, zobacz [Co to jest Zapora aplikacji sieci Web platformy Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Przykłady

Poniższe przykłady przedstawiają wyniki niektórych z tych kombinacji reguł.

### <a name="example-1"></a>Przykład 1

Połączenie z google.com jest dozwolone z powodu pasującej reguły sieciowej.

**Reguła sieci**

- Akcja: Zezwalaj


|name  |Protocol (Protokół)  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Zezwalaj na sieć Web     |TCP|Adres IP|*|Adres IP|*|80 443

**Reguła aplikacji**

- Akcja: Odmów

|name  |Typ źródła  |Element źródłowy  |Protokół:Port|Docelowe nazwy FQDN|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |Adres IP|*|http:80,https:443|google.com

**Wynik**

Połączenie z google.com jest dozwolone, ponieważ pakiet jest zgodny z regułą *sieci zezwalaj na sieć sieci web.* Przetwarzanie reguł zatrzymuje się w tym momencie.

### <a name="example-2"></a>Przykład 2

Ruch SSH jest odrzucany, ponieważ wyższy priorytet *Odmawia zbieranie* reguł sieciowych blokuje go.

**Zbieranie reguł sieciowych 1**

- Nazwa: Zezwalaj na zbieranie
- Priorytet: 200
- Akcja: Zezwalaj

|name  |Protocol (Protokół)  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Zezwalaj na SSH     |TCP|Adres IP|*|Adres IP|*|22

**Zbieranie reguł sieciowych 2**

- Nazwa: Deny-collection
- Priorytet: 100
- Akcja: Odmów

|name  |Protocol (Protokół)  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH (Odmów-SSH)     |TCP|Adres IP|*|Adres IP|*|22

**Wynik**

Połączenia SSH są odrzucane, ponieważ kolekcja reguł sieci o wyższym priorytecie blokuje ją. Przetwarzanie reguł zatrzymuje się w tym momencie.

## <a name="rule-changes"></a>Zmiany reguł

Jeśli zmienisz regułę, aby odmówić wcześniej dozwolonego ruchu, wszystkie odpowiednie istniejące sesje zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć i skonfigurować Zaporę platformy Azure](tutorial-firewall-deploy-portal.md).