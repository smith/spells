# Spells

### render the spells to scripts

```shell
find spells/ -name "*.json" -exec basename \{} .json \; | xargs -I{} bash -c "curl -X DELETE https://spellcaster.sh/spells/{}; curl -s -X POST https://spellcaster.sh --data-binary @spells/{}.json -H 'Content-Type: application/json' | tee scripts/{}.sh"
```

### build a vm using qemu/virt-install on Ubuntu for testing (could do mac too)

```shell
export VM=elastic-jammy-01 IMG=https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img OS=ubuntu22.04; curl https://away.spellcaster.sh/spells/cwitch | bash && scp -o StrictHostKeyChecking=no -i ./id_ed25519 -r scripts $USER@$(virsh domifaddr $VM | awk -F'[ /]+' '{if (NR>2) print $5}'):/home/$USER && ssh -t -i ./id_ed25519 -o StrictHostKeyChecking=no $USER@$(virsh domifaddr $VM | awk -F'[ /]+' '{if (NR>2) print $5}')
```

### destroy vm

```shell
ssh-keygen -f "$HOME/.ssh/known_hosts" -R "$(virsh domifaddr $VM | awk -F'[ /]+' '{if (NR>2) print $5}')" && virsh destroy $VM; virsh undefine $VM --remove-all-storage && rm $VM-cloud-init
```