# Mandant A – Argo-CD-Demo

Argo CD synchronisiert `apps/demo` aus Branch `main` in `mandant-a-dev` auf Cluster 2.
Die Plattformadministration verwaltet das AppProject und die initiale Application
`mandant-a-demo` über `rke2-tenants-lab`; dieses Repository besitzt nur die Anwendung.

## Demo ansehen

Mit DNS-Eintrag `mandant-a-demo.berger.ph` → `192.168.1.195`:
http://mandant-a-demo.berger.ph (reine Lab-Demoseite, keine Zugangsdaten).
Ohne DNS lässt sich die Seite bereits prüfen:

```bash
curl --resolve mandant-a-demo.berger.ph:80:192.168.1.195 http://mandant-a-demo.berger.ph/
# Oder mit der eigenen Rancher-Kubeconfig:
kubectl -n mandant-a-dev port-forward service/tenant-demo 8080:80
# Anschließend http://localhost:8080
```

## GitOps ausprobieren

1. In `apps/demo/configmap.yaml` den Seitentext oder die Versionsanzeige ändern.
2. Committen und nach `origin/main` pushen.
3. In https://argocd.berger.ph die Anwendung `mandant-a-demo` öffnen.
   Automatische Synchronisierung und Self-Heal sind aktiv; Entfernen aus Git entfernt
   mit Prune auch die zugehörige Anwendungsressource.
4. Die eingebundene ConfigMap wird im laufenden Pod aktualisiert. Das kann zusätzlich
   zum Git-Abgleich etwa ein bis zwei Minuten dauern; danach die Seite neu laden.

Zwei kleine Nginx-Pods, keine persistenten Anwendungsdaten. Das Image ist per Digest
fixiert. Namespaces/Projektrechte, Cluster-Ressourcen und Plattformdienste gehören nicht
in dieses Repository. Die Allowlist des AppProjects setzt diese Grenze für Argo-Syncs.
