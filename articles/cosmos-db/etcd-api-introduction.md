---
title: Wprowadzenie do usługi Azure Cosmos DB etcd interfejsu API
description: Ten artykuł zawiera przegląd i kluczowe zalety etcd interfejsu API w usłudze Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205800"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Wprowadzenie do usługi Azure Cosmos DB etcd interfejsu API (wersja zapoznawcza)

Usługa Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych firmy Microsoft dla aplikacji o kluczowym znaczeniu. Ona gotowa do użycia funkcja dystrybucji globalnej, elastyczne skalowanie przepływności i pamięci, milisekundowe opóźnienia w 99. percentylu, oferuje i gwarantowaną wysoką dostępność, wspierane przez wiodących w branży umowy SLA.

[Etcd](https://github.com/etcd-io/etcd) jest magazynem rozproszonych klucz/wartość. W [Kubernetes](https://kubernetes.io/), etcd jest używany do przechowywania stanu i konfiguracji klastrów Kubernetes. Zapewnienie dostępności, niezawodności i wydajności etcd ma podstawowe znaczenie dla ogólnej kondycji klastra, skalowalność, elastyczność dostępności i wydajności klastra Kubernetes. 

Etcd interfejsu API w usłudze Azure Cosmos DB pozwala na używanie usługi Azure Cosmos DB jako magazynu zaplecza dla [Azure Kubernetes](../aks/index.yml). etcd interfejsu API w usłudze Azure Cosmos DB jest obecnie dostępna w wersji zapoznawczej. Usługa Azure Cosmos DB implementuje protokół przewodowy etcd. Za pomocą etcd interfejsu API w usłudze Azure Cosmos DB, deweloperzy automatycznie pobiorą i wysoce niezawodne [dostępne](high-availability.md), [globalnie dystrybuowane](distribute-data-globally.md) Kubernetes. Ten interfejs API umożliwia deweloperom skalowanie zarządzania stanem rozwiązania Kubernetes w usłudze PaaS natywne w pełni zarządzana usługa w chmurze. 

> [!NOTE]
> W przeciwieństwie do innych interfejsów API w usłudze Azure Cosmos DB nie można zainicjować obsługi konta interfejsu API usługi etcd za pośrednictwem witryny Azure portal, zestawów SDK lub interfejsu wiersza polecenia. Możesz aprowizować konta interfejsu API usługi etcd przez wdrożenie szablonu usługi Resource Manager. Aby uzyskać szczegółowe instrukcje, zobacz [instrukcjami aprowizacji usługi Azure Kubernetes przy użyciu usługi Azure Cosmos DB](bootstrap-kubernetes-cluster.md) artykułu. Interfejs API etcd usługi Azure Cosmos DB jest obecnie dostępna w ograniczonej wersji zapoznawczej. Możesz [skorzystania z wersji zapoznawczej](https://aka.ms/cosmosetcdapi-signup), wypełniając formularz rejestracji.

## <a name="wire-level-compatibility"></a>Poziom zgodności o komunikacji sieciowej

Usługa Azure Cosmos DB implementuje protokół przewodowy etcd w wersji 3 i umożliwia [węzła głównego](https://kubernetes.io/docs/concepts/overview/components/) API serwerów do korzystania z usługi Azure Cosmos DB, podobnie jak w w środowisku etcd zainstalowane lokalnie. Etcd interfejs API obsługuje wzajemnego uwierzytelniania protokołu TLS. 

Na poniższym diagramie przedstawiono składniki klastra Kubernetes. We wzorcu klastra serwera interfejsu API używa interfejsu API usługi Azure Cosmos DB etcd zamiast etcd zainstalowane lokalnie. 

![Usługa Azure Cosmos DB, implementowanie protokół przewodowy etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="no-etcd-operations-management"></a>Brak etcd operacji zarządzania

Jako usługę w pełni zarządzana usługa w chmurze natywnej usługi Azure Cosmos DB eliminuje potrzebę dla deweloperów Kubernetes skonfigurować i zarządzać nimi etcd. Etcd interfejsu API w usłudze Azure Cosmos DB jest skalowalne, wysoko dostępną, odporność na uszkodzenia i oferuje wysoką wydajność. Obciążenie konfigurowania replikacji w wielu węzłach, wykonując stopniowe aktualizacje, poprawki zabezpieczeń i monitorowania kondycji etcd są obsługiwane przez usługę Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Dystrybucja globalna i o wysokiej dostępności 

Za pomocą interfejsu API etcd, Azure Cosmos DB gwarantuje dostępność na poziomie 99,99% dla odczytów danych i zapisuje w obrębie jednego regionu i dostępność przez 99,999% w wielu regionach. 

### <a name="elastic-scalability"></a>Elastyczna skalowalność

Usługa Azure Cosmos DB oferuje elastyczną skalowalność do odczytu i zapisu żądania w różnych regionach.
Wraz z rozwojem klastra Kubernetes konta etcd interfejsu API w usłudze Azure Cosmos DB elastycznie skalowanej bez żadnych przestojów. Przechowywanie danych etcd w usłudze Azure Cosmos DB, a nie węzłów głównych Kubernetes umożliwia również bardziej elastyczne skalowanie węzła głównego. 

### <a name="security--enterprise-readiness"></a>Gotowość zabezpieczeń i przedsiębiorstwo

Gdy etcd dane są przechowywane w usłudze Azure Cosmos DB, deweloperzy Kubernetes automatycznie uzyskują [wbudowane szyfrowanie danych magazynowanych](database-encryption-at-rest.md), [certyfikacji i zgodności](compliance.md), i [kopia zapasowa i przywracanie możliwości](online-backup-and-restore.md) obsługiwane przez usługę Azure Cosmos DB. 

## <a name="next-steps"></a>Kolejne kroki

* [Jak używać usługi Azure Kubernetes za pomocą usługi Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Najważniejszych korzyści zapewnianych przez usługę Azure Cosmos DB](introduction.md)
* [Przewodnik Szybki Start aparat usługi AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)