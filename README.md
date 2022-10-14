# arch_nvidia-470xx-utils
PKGBUILD do driver [nvidia-470xx-utils (AUR)](https://aur.archlinux.org/packages/nvidia-470xx-utils) com path para usar noo Kernel 6.0

### Instruções para instalar:

```bash
cd ~/Downloads
git clone https://aur.archlinux.org/nvidia-470xx-utils.git
cd nvidia-470xx-utils
mv PKGBUILD PKGBUILD.BKP
curl -O https://raw.githubusercontent.com/elppans/arch_nvidia-470xx-utils/main/PKGBUILD
makepkg -siLc --needed --noconfirm
sudo nvidia-xconfig
```

### Configurando o GRUB:

Edite o arquivo `/etc/default/grub` e adicione estas opções na variável de comandos:  

```bash
i915.modeset=0 nouveau.modeset=0 nvidia-drm.modeset=1
```

Exemplo de como fica:  

> GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet i915.modeset=0 nouveau.modeset=0 nvidia-drm.modeset=1"

Depois de salvar e sair do arquivo, execute:  

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Configurando o módulo no initramfs:

Adicione os módulos no arquivo `/etc/mkinitcpio.conf`:

```bash
nvidia nvidia_modeset nvidia_uvm nvidia_drm
```

Exemplo:  

> MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)


Depois de salvar e sair do arquivo, execute:  

```bash
sudo /usr/bin/mkinitcpio -P
```

### Configurando regras de inicialização:

Para usar o Wayland em vez do XORG, no Gnome com GDM, após instalar o driver NVidia, edite o arquivo:

> ***/usr/lib/udev/rules.d/61-gdm.rules***

E onde está a seguinte configuração:

```bash
LABEL="gdm_disable_wayland"
RUN+="/usr/lib/gdm-runtime-config set daemon WaylandEnable false"
GOTO="gdm_end"
```

Troque o parâmetro "***false***" para "***true***". Ficando desta forma:

```bash
LABEL="gdm_disable_wayland"
RUN+="/usr/lib/gdm-runtime-config set daemon WaylandEnable true"
GOTO="gdm_end"
```

Depois reinicie.  

Após reiniciar, na hora de logar clique em seu usuário e depois na engrenagem que aparecerá no canto inferior, e escolha a opção "***Gnome sobre Wayland***". Depois logue em seu sistema.  
Após logar, abra o Terminal faça o comando para ter certeza de que está usando XORG ou Wayland:  


```bash
echo $XDG_SESSION_TYPE
```

Se retornar como resposta:

> wayland

Significa que a configuração deu certo e finalmente está usando NVidia + Wayland no Gnome.

Mais detalhes em [doc-linux, ArchLinux NVidia Legacy + Wayland no Gnome](https://elppans.github.io/doc-linux/archlinux_nvidia_legacy_wayland_gnome)
