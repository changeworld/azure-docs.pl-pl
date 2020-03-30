---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361533"
---
### <a name="database-tier"></a>Warstwa bazy danych

Warstwa Baza danych zawiera wystąpienia bazy danych dla aplikacji. Baza danych może być systemem Oracle DB, Oracle RAC lub Oracle Exadata Database. 

Jeśli wybór jest użycie Oracle DB, wystąpienie bazy danych mogą być wdrażane na platformie Azure za pośrednictwem obrazów Oracle DB dostępnych w portalu Azure Marketplace. Alternatywnie można użyć połączenia między platformą Azure i OCI do wdrożenia oracle DB w modelu PaaS na OCI.

W przypadku rozwiązania Oracle RAC można wdrożyć program Oracle RAC w usłudze Azure CloudSimple w modelu IaaS lub w modelu OCI w modelu PaaS. Zaleca się użycie dwuwęzłowego systemu RAC. 

Wreszcie, w przypadku systemów Exadata, użyj połączenia OCI i wdrożyć system Exadata w OCI. Powyższy diagram architektury przedstawia system Exadata wdrożony w OCI w dwóch podsieciach.

W scenariuszach produkcyjnych wdrożyć wiele wystąpień bazy danych w dwóch strefach dostępności (jeśli wdrażanie na platformie Azure) lub dwie domeny dostępności (w OCI). Oracle Active Data Guard służy do synchronizowania baz danych podstawowych i rezerwowych.

Warstwa bazy danych odbiera tylko żądania z warstwy środkowej. Zaleca się skonfigurowanie sieciowej grupy zabezpieczeń (listy zabezpieczeń w przypadku wdrażania bazy danych w OCI), aby zezwalać na żądania tylko na porcie 1521 z warstwy środkowej i portu 22 z serwera bastionu ze względów administracyjnych.

W przypadku baz danych wdrożonych w OCI należy skonfigurować oddzielną sieć chmury wirtualnej z dynamiczną bramą routingu (DRG), która jest połączona z obwodem FastConnect.