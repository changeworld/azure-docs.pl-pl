<a name="virtual-networking-limits-classic"></a>Poniższe limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu klasycznego modelu wdrażania, które przypadają na jedną subskrypcję.

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |100 |
| Lokalne lokacje sieciowe |20 |kontakt z pomocą techniczną |
| Serwery DNS na sieć wirtualną |20 |100 |
| Prywatne adresy IP na sieć wirtualną |4096 |4096 |
| Współbieżne TCP lub UDP przepływów na kartę Sieciową maszyny wirtualnej lub wystąpienia roli |500 000 |500 000 |
| Sieciowe grupy zabezpieczeń (NSG) |100 |200 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |200 |400 |
| Zdefiniowane przez użytkownika tabele tras |100 |200 |
| Zdefiniowane przez użytkownika trasy na tabelę tras |100 |400 |
| Publiczne adresy IP (dynamiczne) |5 |kontakt z pomocą techniczną |
| Zastrzeżone publiczne adresy IP |20 |kontakt z pomocą techniczną |
| Publiczne adresy VIP na wdrożenie |5 |kontakt z pomocą techniczną |
| Prywatne adresy VIP (wewnętrzny moduł równoważenia obciążenia) na wdrożenie |1 |1 |
| Listy kontroli dostępu do punktu końcowego (ACL) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limity dotyczące sieci — Azure Resource Manager
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję.

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |1000 |
| Podsieci na sieć wirtualną |1000 |10 000 |
| Komunikacji równorzędnych sieci wirtualnych dla sieci wirtualnej |10 |50 |
| Serwery DNS na sieć wirtualną |9 |25 |
| Prywatne adresy IP na sieć wirtualną |4096 |8192 |
| Prywatne adresy IP na interfejs sieciowy |256 |1024 |
| Współbieżne TCP lub UDP przepływów na kartę Sieciową maszyny wirtualnej lub wystąpienia roli |500 000 |500 000 |
| Interfejsy sieciowe (karta sieciowa) |350 |20000 |
| Sieciowe grupy zabezpieczeń (NSG) |100 |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |200 |1000 |
| Adresy IP i zakresów określony dla źródła lub miejsca docelowego w grupie zabezpieczeń |2000 |4000 |
| Grupy zabezpieczeń aplikacji |200 |500 |
| Stosowanie grup zabezpieczeń dla konfiguracji IP dla karty Sieciowej |10 |20 |
| Konfiguracje adresów IP na grupy zabezpieczeń aplikacji |1000 |4000 |
| Grupy zabezpieczeń aplikacji, które można określić w ramach wszystkich reguł zabezpieczeń grupy zabezpieczeń sieci |50 |100 |
| Zdefiniowane przez użytkownika tabele tras |100 |200 |
| Zdefiniowane przez użytkownika trasy na tabelę tras |100 |400 |
| Publiczne adresy IP — dynamiczne |(Basic) 60 |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczne |(Basic) 20 |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczne |(Standard) 20 |kontakt z pomocą techniczną |
| Certyfikaty główne typu punkt-lokacja na bramę VPN Gateway |20 |20 |

#### <a name="load-balancer"></a>Limity usługi równoważenia obciążenia

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Moduły równoważenia obciążenia | 100 | 1000 |
| Zasady dla zasobów, Basic | 150 | 250 |
| Reguły w poszczególnych zasobów i Standard | 1250 | 1500 |
| Zasady na konfigurację adresu IP | 299 |299 |
| Konfiguracje adresów IP frontonu, Basic | 10 | 200 |
| Konfiguracje adresów IP frontonu, Standard | 10 | 600 |
| Puli zaplecza, Basic | 100, pojedynczy zestaw dostępności | 100, pojedynczy zestaw dostępności |
| Puli zaplecza, Standard | 1000, pojedynczy sieci wirtualnej | 1000, pojedynczy sieci wirtualnej |
| HA portów, Standard | 1 na frontonie wewnętrzny | 1 na frontonie wewnętrzny |

Jeśli będzie konieczne zwiększenie limitów domyślnych, [skontaktuj się z pomocą techniczną](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

