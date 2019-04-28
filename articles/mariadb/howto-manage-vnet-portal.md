---
title: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB oraz reguł za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB oraz reguł za pomocą witryny Azure portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6644b6ae3a9482a1bd3f840a814d3bb6361517fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745918"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB oraz reguł sieci wirtualnej przy użyciu witryny Azure portal

Punkty końcowe usługi sieci wirtualnej i reguł dotyczyć usługi Azure Database dla serwera MariaDB prywatnej przestrzeni adresowej sieci wirtualnej. Omówienie usługi Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB, łącznie z ograniczeniami, zobacz [— Azure Database for punkty końcowe usługi sieci wirtualnej serwera MariaDB](concepts-data-access-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionów dla usługi Azure Database dla serwera MariaDB.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Utwórz regułę sieci wirtualnej i włączyć punkty końcowe usługi

1. Na stronie serwera MariaDB, w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** aby otworzyć okienko zabezpieczenia połączeń dla usługi Azure Database dla serwera MariaDB.

2. Upewnij się, że Zezwalaj na dostęp do usług platformy Azure kontrolki jest ustawiona na **OFF**.

> [!Important]
> Jeśli zostanie ustawiona na wartość ON, serwer bazy danych MariaDB Azure akceptuje komunikację z dowolnej podsieci. Pozostawienie formant ustawiony na wartość ON, może być nadmierne dostępu z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi Microsoft Azure Virtual Network, w połączeniu z funkcją reguły sieci wirtualnej usługi Azure Database dla serwera MariaDB, razem może zmniejszyć obszar powierzchni zabezpieczeń.

3. Przejdź do menu **+ Dodawanie istniejącej sieci wirtualnej**. Jeśli nie masz istniejącej sieci wirtualnej możesz kliknąć **+ Utwórz nową sieć wirtualną** ją utworzyć. Zobacz [Szybki start: Tworzenie sieci wirtualnej przy użyciu witryny Azure portal](../virtual-network/quick-create-portal.md)

   ![Portal Azure — zabezpieczenia połączeń kliknij](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, sieć wirtualna i Nazwa podsieci, a następnie kliknij przycisk **Włącz**. Dzięki temu punktów końcowych usługi sieci wirtualnej w podsieci przy użyciu automatycznie **Microsoft.SQL** tag usługi.

   ![Portal Azure — Konfigurowanie sieci wirtualnej](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Konto musi mieć uprawnienia niezbędne do tworzenia sieci wirtualnej i punktu końcowego usługi.

   Punkty końcowe usługi można konfigurować w sieciach wirtualnych niezależnie od siebie, przez użytkownika z dostępem do zapisu do sieci wirtualnej.
    
   Aby zabezpieczyć zasoby usługi platformy Azure z siecią wirtualną, użytkownik musi mieć uprawnienia do "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.
    
   Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Zasoby usługi sieci wirtualnej i na platformie Azure należą do różnych subskrypcji, zasoby, powinny znajdować się w tej samej dzierżawie usługi Active Directory (AD).

   > [!IMPORTANT]
   > Zdecydowanie zaleca się przeczytaj ten artykuł, konfiguracje punktu końcowego usługi i zagadnienia, przed rozpoczęciem konfigurowania punktów końcowych usługi. **Punkt końcowy usługi wirtualne sieci:** A [punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi platformy Azure. Punkty końcowe usługi sieci wirtualnej, użyj nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi platformy Azure o nazwie bazy danych SQL. Ten tag usługi dotyczy także usługi Azure SQL Database, Azure Database dla usługi MariaDB, PostgreSQL i MySQL. Ważne jest, aby pamiętać podczas stosowania **Microsoft.Sql** tag usługi punktu końcowego usługi sieci wirtualnej umożliwia skonfigurowanie ruch w ramach punktu końcowego usługi dla wszystkich usług bazy danych Azure, w tym usługi Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB oraz usługi Azure Database for MySQL serwerów w podsieci.
   > 

5. Po włączeniu kliknij **OK** i zobaczysz, że punkty końcowe usługi sieci wirtualnej są włączone wraz z reguły sieci wirtualnej.

   ![Włączone punkty końcowe usługi sieci wirtualnej i regułę sieci wirtualnej, utworzyć](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [Konfigurowanie protokołu SSL w usłudze Azure Database dla serwera MariaDB](howto-configure-ssl.md)
- Podobnie, można utworzyć skrypty do [Włącz sieć wirtualną punkty końcowe usługi i Utwórz regułę sieci Wirtualnej dla usługi Azure Database dla serwera MariaDB przy użyciu wiersza polecenia platformy Azure](howto-manage-vnet-cli.md).
