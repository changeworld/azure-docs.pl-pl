---
title: Wprowadzenie do interfejsu API usługi Azure Cosmos DB itp.
description: Ten artykuł zawiera omówienie i najważniejsze korzyści z interfejsu API etcd w usłudze Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498594"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Wprowadzenie do interfejsu API usługi Azure Cosmos DB etcd (wersja zapoznawcza)

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Oferuje globalną dystrybucję "pod klucz", elastyczne skalowanie przepływności i pamięci masowej, jednocyfrowe opóźnienia milisekundy na 99 percentylu i gwarantowaną wysoką dostępność, wszystkie wspierane przez wiodące w branży umowy SLA.

[Etcd](https://github.com/etcd-io/etcd) jest rozproszonym kluczem / magazynem wartości. W [umięsienia,](https://kubernetes.io/)etcd jest używany do przechowywania stanu i konfiguracji klastrów Kubernetes. Zapewnienie dostępności, niezawodności i wydajności etcd ma kluczowe znaczenie dla ogólnej kondycji klastra, skalowalności, elastyczności dostępności i wydajności klastra Kubernetes. 

Interfejs API etcd w usłudze Azure Cosmos DB umożliwia używanie usługi Azure Cosmos DB jako magazynu zaplecza dla [platformy Azure Kubernetes.](../aks/index.yml) interfejs API etcd w usłudze Azure Cosmos DB jest obecnie w wersji zapoznawczej. Usługa Azure Cosmos DB implementuje protokół przewodowy etcd. Dzięki interfejsowi API etcd w usłudze Azure Cosmos DB deweloperzy automatycznie otrzymają wysoce niezawodne, [dostępne,](high-availability.md) [globalnie rozproszone](distribute-data-globally.md) kubernetes. Ten interfejs API umożliwia deweloperom skalowanie zarządzania stanem Kubernetes w pełni zarządzaną natywną usługę PaaS w chmurze. 

> [!NOTE]
> W przeciwieństwie do innych interfejsów API w usłudze Azure Cosmos DB nie można aprowizować konta interfejsu API etcd za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia lub zestawów SDK. Można aprowizować konto interfejsu API etcd, wdrażając tylko szablon Menedżera zasobów; aby uzyskać szczegółowe kroki, zobacz [Jak aprowizować usługi Azure Kubernetes za pomocą usługi Azure Cosmos DB](bootstrap-kubernetes-cluster.md) artykuł. Interfejs API usługi Azure Cosmos DB etcd jest obecnie w ograniczonej wersji zapoznawczej. Możesz [zapisać się do podglądu,](https://aka.ms/cosmosetcdapi-signup)wypełniając formularz rejestracji.

## <a name="wire-level-compatibility"></a>Zgodność z poziomem przewodów

Usługa Azure Cosmos DB implementuje protokół przewodowy etcd w wersji 3 i umożliwia serwerom interfejsu API [węzła głównego](https://kubernetes.io/docs/concepts/overview/components/) używanie usługi Azure Cosmos DB, tak jak miałoby to nastąpić w środowisku zainstalowanym lokalnie itp. Interfejs API etcd obsługuje wzajemne uwierzytelnianie TLS. 

Na poniższym diagramie przedstawiono składniki klastra Kubernetes. W wzorcu klastra serwer interfejsu API używa interfejsu API usługi Azure Cosmos DB etcd, zamiast lokalnie zainstalowanych itpd. 

![Usługa Azure Cosmos DB implementująca protokół przewodowy etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="no-etcd-operations-management"></a>Brak zarządzania operacjami etcd

Jako w pełni zarządzana natywna usługa w chmurze usługa Azure Cosmos DB eliminuje potrzebę konfigurowania i zarządzania przez deweloperów usługi Microsoft. Interfejs API etcd w usłudze Azure Cosmos DB jest skalowalny, wysoce dostępny, odporny na uszkodzenia i oferuje wysoką wydajność. Obciążenie związane z konfigurowaniem replikacji w wielu węzłach, wykonywaniem aktualizacji stopniowych, poprawkami zabezpieczeń i monitorowaniem kondycji etcd są obsługiwane przez usługę Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Globalna dystrybucja & wysoką dostępność 

Za pomocą interfejsu API etcd usługi Azure Cosmos DB gwarantuje dostępność 99,99% dla odczytów i zapisów danych w jednym regionie i 99,999% dostępności w wielu regionach. 

### <a name="elastic-scalability"></a>Elastyczna skalowalność

Usługa Azure Cosmos DB oferuje elastyczną skalowalność żądań odczytu i zapisu w różnych regionach.
Wraz z rozwojem klastra Kubernetes konto interfejsu API etcd w usłudze Azure Cosmos DB elastycznie skaluje się bez żadnych przestojów. Przechowywanie danych etcd w usłudze Azure Cosmos DB, zamiast węzłów głównych usługi Kubernetes umożliwia również bardziej elastyczne skalowanie węzłów głównych. 

### <a name="security--enterprise-readiness"></a>Bezpieczeństwo & gotowość przedsiębiorstwa

Gdy dane etcd są przechowywane w usłudze Azure Cosmos DB, deweloperzy usługi Kubernetes automatycznie otrzymują [wbudowane szyfrowanie w spoczynku,](database-encryption-at-rest.md) [certyfikaty i zgodność](compliance.md)oraz [funkcje tworzenia kopii zapasowych i przywracania](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) obsługiwane przez usługę Azure Cosmos DB. 

## <a name="next-steps"></a>Następne kroki

* [Jak korzystać z usługi Azure Kubernetes z usługą Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Najważniejsze zalety usługi Azure Cosmos DB](introduction.md)
* [Przewodnik szybki start silnika AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)