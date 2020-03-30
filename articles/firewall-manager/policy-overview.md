---
title: Omówienie zasad usługi Azure Firewall Manager Preview
description: Dowiedz się więcej o zasadach usługi Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77445021"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Omówienie zasad usługi Azure Firewall Manager Preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Zasady zapory to zasób platformy Azure zawierający kolekcje reguł sieciowej, sieciowej i reguł aplikacji, a także ustawienia analizy zagrożeń. Jest to zasób globalny, który może być używany w wielu wystąpieniach zapory platformy Azure w zabezpieczonych centrach wirtualnych i sieciach wirtualnych koncentratora. Zasady działają w różnych regionach i subskrypcjach.

![Zasady usługi Azure Firewall Manager](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Tworzenie i stowarzyszenie polityki

Zasady można tworzyć i zarządzać nimi na wiele sposobów, w tym w witrynie Azure portal, interfejs API REST, szablony, program Azure PowerShell i interfejs wiersza polecenia.

Można również migrować istniejące reguły z Zapory platformy Azure przy użyciu portalu lub programu Azure PowerShell do tworzenia zasad. Aby uzyskać więcej informacji, zobacz [Jak migrować konfiguracje Zapory platformy Azure do zasad Zapory Platformy Azure (wersja zapoznawcza)](migrate-to-policy.md). 

Zasady mogą być skojarzone z co najmniej jednym koncentratorem wirtualnym lub sieciami wirtualnymi. Zapora może znajdować się w dowolnej subskrypcji skojarzonej z Twoim kontem i w dowolnym regionie.

## <a name="hierarchical-policies"></a>Zasady hierarchiczne

Nowe zasady można tworzyć od podstaw lub dziedziczyć z istniejących zasad. Dziedziczenie umożliwia DevOps tworzenie zasad zapory lokalnej na podstawie zasad podstawowych zlecanym organizacji.

Zasady utworzone przy niepustych zasadach nadrzędnych dziedziczą wszystkie kolekcje reguł z zasad nadrzędnych. Kolekcje reguł sieciowych dziedziczone z zasad nadrzędnych są zawsze priorytetowe niż kolekcje reguł sieciowych zdefiniowane jako część nowej zasady. Ta sama logika ma również zastosowanie do kolekcji reguł aplikacji. Jednak kolekcje reguł sieciowych są zawsze przetwarzane przed kolekcjami reguł aplikacji, niezależnie od dziedziczenia.

Tryb analizy zagrożeń jest również dziedziczona z zasad nadrzędnych. Tryb analizy zagrożeń można ustawić na inną wartość, aby zastąpić to zachowanie, ale nie można go wyłączyć. Jest to możliwe tylko do zastąpienia z bardziej rygorystyczne wartości. Jeśli na przykład zasady nadrzędne są ustawione tylko na **Alert,** można skonfigurować tę zasadę lokalną na **Alert i odmów.**

Kolekcje reguł TRANSLATORA nie są dziedziczone, ponieważ są specyficzne dla danej zapory.

W przypadku dziedziczenia wszelkie zmiany w zasadach nadrzędnych są automatycznie stosowane do skojarzonych zasad podrzędnych zapory.

## <a name="traditional-rules-and-policies"></a>Tradycyjne zasady i polityki

Zapora azure obsługuje zarówno tradycyjne reguły, jak i zasady. W poniższej tabeli porównano zasady i reguły:


|         |Zasady  |Reguły  |
|---------|---------|---------|
|Contains     |Ustawienia translatora i sieci, sieci, aplikacji i analizy zagrożeń|Reguły translatora i sieci kontaktów, sieci i aplikacji |
|Chroni     |Koncentratory wirtualne i sieci wirtualne|Tylko sieci wirtualne|
|Środowisko portalu     |Centralne zarządzanie za pomocą Menedżera zapory|Autonomiczna zapora|
|Obsługa wielu zapór sieciowych     |Zasady zapory to oddzielny zasób, który może być używany w zapory|Ręczne eksportowanie i importowanie reguł lub korzystanie z rozwiązań do zarządzania innych firm |
|Cennik     |Rozliczane na podstawie skojarzenia zapory. Zobacz [Cennik](#pricing).|Bezpłatna|
|Obsługiwane mechanizmy wdrażania     |Portal, interfejs API REST, szablony, program Azure PowerShell i interfejs wiersza polecenia|Portal, interfejs API REST, szablony, program PowerShell i interfejs wiersza polecenia. |
|Stan wydania     |Publiczna wersja zapoznawcza|Ogólna dostępność|

## <a name="pricing"></a>Cennik

Zasady są rozliczane na podstawie skojarzeń zapory. Zasady z zerowym lub jednym skojarzeniem zapory są bezpłatne. Zasady z wieloma skojarzeniami zapory są rozliczane według stałej stawki. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć Zaporę platformy Azure, zobacz [Samouczek: Zabezpiecz sieć w chmurze za pomocą usługi Azure Firewall Manager Preview przy użyciu witryny Azure portal](secure-cloud-network.md).
