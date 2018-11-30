---
title: Włączanie szyfrowania dla konta magazynu w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób implementowania zaleceń Centrum zabezpieczeń Azure **Włączanie szyfrowania dla konta usługi Azure Storage**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7bd6b6fa82d2c7c8129f7e88df6803b697bfc6d6
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307422"
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Włączanie szyfrowania dla konta usługi Azure storage w usłudze Azure Security Center
Usługa Azure Security Center może zalecić włączyć szyfrowanie usługi Azure Storage dla danych magazynowanych.

Szyfrowanie usługi Storage (SSE) polega na szyfrowanie danych są zapisywane do usługi Azure storage oraz odszyfrowywanie danych przed pobierania.  SSE jest obecnie dostępna tylko w przypadku usługi Azure Blob service i może służyć do blokowych obiektów blob, stronicowe obiekty BLOB i uzupełnialnych obiektów blob.  Aby dowiedzieć się więcej, zobacz [szyfrowanie usługi Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).


> [!Note]
> Po włączeniu szyfrowania tylko nowe dane są szyfrowane. Wszystkie istniejące obiekty BLOB na koncie magazynu pozostaną niezaszyfrowane. Aby zaszyfrować istniejących obiektów blob, zobacz [często zadawane pytania dotyczące magazynu usługi szyfrowania](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

Szyfrowanie usługi Storage jest obsługiwana tylko na kontach magazynu usługi Resource Manager. Klasyczne konta magazynu nie są obecnie obsługiwane. Aby poznać klasyczny, jak i modelem wdrażania usługi Resource Manager, zobacz [modele wdrażania Azure](../azure-classic-rm.md).

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **Włączanie szyfrowania dla konta usługi Azure Storage**.
   ![Włączanie szyfrowania dla konta magazynu][1]
2. **Włącz szyfrowanie magazynu** zostanie otwarty blok. Ten blok zawiera listę kont usługi Azure storage, gdzie szyfrowanie magazynu jest wyłączone. W tym przykładzie wybierzemy **storageacct1**.
   ![Włącz szyfrowanie magazynu][2]
3. **Szyfrowania** bloku **storageacct1** zostanie otwarty. Wybierz **włączone**.
   ![Blok szyfrowania][3]
4. Wybierz pozycję **Zapisz**.

Teraz włączono szyfrowanie magazynu **storageacct1**.


## <a name="see-also"></a>Zobacz także
W tym dokumencie pokazano sposób implementacji zalecenia usługi Security Center "Włącz szyfrowanie dla konta magazynu platformy Azure". Aby dowiedzieć się więcej o funkcji szyfrowanie usługi Storage platformy Azure, zobacz następujące tematy:

* [Szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-azure-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
