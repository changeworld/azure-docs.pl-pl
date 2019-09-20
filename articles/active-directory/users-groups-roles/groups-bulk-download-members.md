---
title: Lista członkostwa w grupie pobierania zbiorczego — Portal Azure Active Directory | Microsoft Docs
description: Dodaj użytkowników zbiorczo w centrum administracyjnym platformy Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c2ac1d662851b5a0b44ec475becb5f5e0403c4
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146395"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Zbiorcze pobieranie elementów członkowskich grupy (wersja zapoznawcza) w Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można zbiorczo pobierać elementy członkowskie grupy w organizacji do pliku z wartościami rozdzielanymi przecinkami (CSV).

> [!NOTE]
> Operacje zbiorcze usługi Azure AD są publiczną funkcją w wersji zapoznawczej usługi Azure AD i są dostępne z dowolnym płatnym planem licencjonowania usługi Azure AD. Aby uzyskać więcej informacji na temat terminów korzystania z wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania programu Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)wersjach zapoznawczych.

## <a name="to-bulk-download-group-membership"></a>Do pobierania zbiorczego członkostwa w grupie

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników w organizacji. Właściciele grupy mogą również zbiorczo pobierać członków grup, których są właścicielami.
1. W usłudze Azure AD wybierz kolejno pozycje **grupy** > **wszystkie grupy**.
1. Otwórz grupę, której członkostwo chcesz pobrać, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Pobierz członków** , aby pobrać plik CSV zawierający listę elementów członkowskich grupy.

   ![Polecenie Pobierz elementy członkowskie znajduje się na stronie profilu dla grupy](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Sprawdź stan pobierania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Na stronie wyniki operacji zbiorczych jest wyświetlany stan zbiorczego żądania](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze służące do pobierania listy członków grupy może być uruchamiane przez maksymalnie jedną godzinę. Dzięki temu można pobrać listę co najmniej 500 000 członków.

## <a name="next-steps"></a>Następne kroki

- [Członkowie grupy importu zbiorczego](groups-bulk-import-members.md)
- [Zbiorcze usuwanie członków grupy](groups-bulk-download-members.md)
