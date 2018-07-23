| Zasób | Limit domyślny |
| --- | :--- |
| Maksymalna liczba węzłów w klastrze | 100 |
| Maksymalna liczba zasobników w węźle ([sieć podstawowa z rozwiązaniem Kubenet][basic-networking]) | 110 |
| Maksymalna liczba zasobników w węźle ([sieć zaawansowana z rozwiązaniem Azure CNI][advanced-networking]) | 30<sup>1</sup> |
| Maksymalna liczba klastrów w subskrypcji | 100 |

<sup>1</sup> Tę wartość można dostosować przez wdrożenie szablonu usługi ARM. Zobacz przykłady [tutaj][arm-deployment-example].<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
