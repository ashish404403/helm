# helm

# Installing Helm

# From Script

    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
    chmod 700 get_helm.sh
    ./get_helm.sh

# From Apt (Debian/Ubuntu)

    HELM_BUILDKITE_APT_KEY_ID="DDF78C3E6EBB2D2CC223C95C62BA89D07698DBC6"
    
    sudo apt-get install curl gpg apt-transport-https --yes

    curl -fsSL https://packages.buildkite.com/helm-linux/helm-debian/gpgkey > "${TMPDIR:-/tmp}/helm.gpg"
    
    # Ensure that the key ID matches to prevent a repository compromise from establishing an attacker controlled key
    if [ "$(gpg --show-keys --with-colons "${TMPDIR:-/tmp}/helm.gpg" | awk -F: '$1 == "fpr" {print $10}' | head -n 1)" != "${HELM_BUILDKITE_APT_KEY_ID}" ]; then echo "ERROR: Unexpected Helm APT key ID: potential key compromise"; exit 1; fi
    
    cat "${TMPDIR:-/tmp}/helm.gpg" | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
    echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://packages.buildkite.com/helm-linux/helm-debian/any/ any main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
    
    sudo apt-get update
    sudo apt-get install helm

# Create Helm chart
    helm create playground

# Validate Helm chart
    helm lint playground

# dry-run & debug
    helm install myplayground --debug --dry-run playground

#
#

# helm install
    helm install myplayground playground

# Common variations:

# Install into a specific namespace, creating it if missing
    helm install myplayground playground -n myapp --create-namespace

# Override values at install time
    helm install myplayground playground --set replicaCount=3

# Use a custom values file
    helm install myplayground playground -f custom-values.yaml
#
#

# helm list
    helm list

#
#

# helm upgrade
    helm upgrade myplayground playground
    
# Combine with --install to make it idempotent (install if not present, upgrade if it is):
    helm upgrade --install myplayground playground

#
    helm upgrade myplayground playground --set image.tag=v2.0.0
    helm upgrade myplayground playground -f values-prod.yaml
    helm upgrade myplayground playground --atomic   # auto-rollback on failure

#
#

# helm rollback
    helm rollback myplayground 1

#
    helm history myplayground

# helm uninstall
    helm uninstall myplayground

# Keep history for potential rollback by adding --keep-history (release stays visible in helm list -a but resources are removed):
    helm uninstall myplayground --keep-history

# helm repo
    helm repo list

#
    helm repo add bitnami https://charts.bitnami.com/bitnami   # add a repo
    helm repo update                                              # refresh cached chart index
    helm repo remove bitnami                                      # remove a repo

# helm search hub
    helm search hub postgresql

# search only YOUR added local repos (needs `helm repo update` first)
    helm search repo postgresql

