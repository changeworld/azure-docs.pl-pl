---
title: Tworzenie i zarządzanie Azure bazy danych dla punktów końcowych usługi sieci wirtualnej PostgreSQL i reguł za pomocą portalu Azure | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie Azure bazy danych dla punktów końcowych usługi sieci wirtualnej PostgreSQL i reguł za pomocą portalu Azure
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 8199bd98b9ae091ff2b27efa8334e8e763879359
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757369"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie nimi Azure bazy danych dla punktów końcowych usługi sieci wirtualnej PostgreSQL i zasad sieci wirtualnej przy użyciu portalu Azure
Wirtualne punktów końcowych usługi Network (VNet) i reguły rozszerzają prywatnej przestrzeni adresowej sieci wirtualnej do bazy danych Azure, PostgreSQL serwera. Omówienie bazy danych Azure dla punktów końcowych usługi PostgreSQL sieci wirtualnej, w tym ograniczenia, zobacz [bazą danych Azure dla punktów końcowych usługi sieci wirtualnej serwera PostgreSQL](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne w publicznej wersji zapoznawczej w wszystkie obsługiwane regiony dla bazy danych Azure dla PostgreSQL.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Utwórz regułę sieci wirtualnej i Włącz punktów końcowych usługi w portalu Azure

1. Na stronie serwer PostgreSQL, w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** otworzyć okienko połączenia zabezpieczeń bazy danych Azure do PostgreSQL. Przejdź do menu **+ Dodawanie istniejącej sieci wirtualnej**. Jeśli nie masz istniejącej sieci wirtualnej możesz kliknąć **+ Utwórz nową sieć wirtualną** go utworzyć. Zobacz [Szybki Start: tworzenie sieci wirtualnej przy użyciu portalu Azure](../virtual-network/quick-create-portal.md)

   ![Portal Azure — kliknij przycisk Zabezpieczenia połączeń](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, sieci wirtualnych i nazwy podsieci, a następnie kliknij przycisk **włączyć**. Dzięki temu automatycznie punktów końcowych usługi sieci wirtualnej przy użyciu podsieci **Microsoft.SQL** usługi tagu.

   ![Portal Azure — Konfigurowanie sieci wirtualnej](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Zdecydowanie zaleca się w niniejszym artykule konfiguracji punktu końcowego usługi i zaleceń dotyczących przed rozpoczęciem konfigurowania punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** A [punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) podsieć, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi Azure. Punkty końcowe usługi sieci wirtualnej, użyj nazwy typu usługi **Microsoft.Sql**, które odwołuje się do usługi Azure o nazwie bazy danych SQL. Ten numer seryjny usługi ma również zastosowanie do usługi Azure SQL Database, baza danych Azure dla usług PostgreSQL i MySQL. Ważne jest, aby podczas stosowania należy uwzględnić **Microsoft.Sql** usługi tagu dla punktu końcowego sieci wirtualnej, ponieważ konfiguruje ruch punkt końcowy usługi dla wszystkich usług Azure bazy danych, w tym usługi Azure SQL Database, baza danych Azure dla PostgreSQL i Baza danych Azure, serwerów MySQL w podsieci. 
   > 

3. Po włączeniu kliknij **OK** i wyświetlany jest włączenie punktów końcowych usługi sieci wirtualnej wraz z reguły sieci wirtualnej.

   ![Włączone punktów końcowych usługi sieci wirtualnej i utworzyć regułę sieci wirtualnej](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Kolejne kroki
- Podobnie można utworzyć skrypty do [włączyć sieci wirtualnej punkty końcowe usługi i Utwórz regułę sieci Wirtualnej Azure bazy danych dla PostgreSQL przy użyciu interfejsu wiersza polecenia Azure](howto-manage-vnet-using-cli.md).
- Aby uzyskać pomoc w połączeniu z bazą danych PostgreSQL serwera Azure, zobacz [biblioteki połączeń dla bazy danych Azure dla PostgreSQL](./concepts-connection-libraries.md)
