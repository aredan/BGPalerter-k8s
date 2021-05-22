# BGPalerter - k8s edition.
This is a deployment file to run BGPalerter in a Kubernetes cluster.


Self-configuring BGP monitoring tool, which allows you to monitor in **real-time** if:
* any of your prefixes loses visibility;
* any of your prefixes is hijacked;
* your AS is announcing RPKI invalid prefixes (e.g., not matching prefix length);
* your AS is announcing prefixes not covered by ROAs;
* ROAs covering your prefixes are no longer reachable (e.g., TA malfunction);
* a ROA involving any of your prefixes or ASes was deleted/added/edited;
* your AS is announcing a new prefix that was never announced before;
* one of the AS paths used to reach your prefix matches a specific condition defined by you.

https://github.com/nttgin/BGPalerter/edit/master/README.md

# Deploying it into k8s.

First you need to create two configMaps where the config.yml and prefixes.yml will be store, this way the need to persistent volumes is not needed since from my reserarch, this are the only files that need to be saved to have a consistent running BGPalerter deamon.

In a Linux machine:

1. Download the binary:  `wget https://github.com/nttgin/BGPalerter/releases/latest/download/bgpalerter-linux-x64`

2. Download [`config.yml.example`](https://raw.githubusercontent.com/nttgin/BGPalerter/master/config.yml.example) as `config.yml` (in the same directory of the binary)

3. Make the binary executable (e.g., `chmod +x bgpalerter-linux-x64`)

4. Auto-configure it: `./bgpalerter-linux-x64 generate -a _YOUR_ASN_ -o prefixes.yml -i -m` 

5. Create the namespace and configMap: 
* kubectl create namespace bgpalerter
* kubectl create cm bgpalerter-config -n bgpalerter --from-file=config.yml
* kubectl create cm bgpalerter-config -n bgpalerter --from-file=prefixes.yml

6. Create the deployment using the file from this repo:
* kubectl aaply -f deployment-bgpalerter.yaml

7. Look for your BGPalerter Pod:
* kubectl get pods -n bgpalerter

8. Checkout for logs:
* kubectl logs <pods> -n bgpalerter




