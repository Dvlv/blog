Title: Linux Ipsum
Date: 2023-12-31
Tags: Linux
Category: Linux

<h3>Generate placeholder text based on the world of Linux.</h3>
<br />
<hr />
<div>
<b>Number of Words:</b>&nbsp;
<input type="number" id="ipsumLength" name="ipsumLength" value="20" style="padding: 10px;font-size:1.2rem;border-radius:5px;"></input>
&nbsp;
<button type="button" onclick="makeIpsum()" style="padding:10px 15px;background:#ff6600;color:white;border-radius:5px;border:1px solid #ff6600;font-size:1.3rem;cursor:pointer;">Generate</button>
</div>
<div>
  <p id="output"></p>
</div>

<script>
const singleWords = ['linux', 'fedora', 'openSUSE', 'debian', 'ubuntu', 'arch', 'distrohopping', 'sudo', 'dnf', 'zypper', 'apt', 'apt-get', 'garuda', 'systemd', 'gentoo', 'void', 'nix', 'immutable', 'vanilla', 'flatpak', 'flathub', 'snap', 'appimage', 'tarball', 'repo', 'repositories', 'xero', 'endeavour', 'manjaro', 'kernel', 'GNU/Linux', 'pop!_os', 'kubuntu', 'xubuntu', 'lubuntu', 'cinnamon', 'GNOME', 'xfce', 'KDE', 'plasma', 'pantheon', 'elementary', 'zorin', 'distrobox', 'podman', 'docker', 'flatseal', 'tux', 'bash', 'terminal', 'wine', 'FOSS', 'bash', 'zsh', 'centos', 'redhat', 'alma', 'rocky', 'oracle', 'canonical', 'selinux', 'pipewire', 'pulseaudio', 'wayland', 'xorg', 'x11', 'firefox'];
const doubleWords = ['package manager', 'linux mint', 'arch (btw)', 'fedora silverblue', 'openSUSE tumbleweed', 'open source', 'command line', 'free software', 'desktop environment', 'window manager', 'display manager'];

function generate(numWords) {
    shuffle(singleWords);
    shuffle(doubleWords);

    var ipsumText = '';
    var wordsLeft = numWords;

    var lastDoubleIdx = 0;
    var lastSingleIdx = 0;
    while (wordsLeft > 0) {
        if (wordsLeft > 1) {
            if (Math.random() > 0.7) {
                // pick the next double word
                if (lastDoubleIdx < doubleWords.length) {
                    ipsumText += doubleWords[lastDoubleIdx] + " ";
                    lastDoubleIdx += 1;
                } else {
                    shuffle(doubleWords);
                    ipsumText += doubleWords[0] + " ";
                    lastDoubleIdx = 1;
                }

                wordsLeft -= 2;
            } else {
                // pick a single word
                if (lastSingleIdx < singleWords.length) {
                    ipsumText += singleWords[lastSingleIdx] + " ";
                    lastSingleIdx += 1;
                } else {
                    shuffle(singleWords);
                    ipsumText += singleWords[0] + " ";
                    lastSingleIdx = 1;
                }

                wordsLeft -= 1;
            }
        } else {
            // pick a single word
            if (lastSingleIdx < singleWords.length) {
                ipsumText += singleWords[lastSingleIdx] + " ";
                lastSingleIdx += 1;
            } else {
                shuffle(singleWords);
                ipsumText += singleWords[0] + " ";
                lastSingleIdx = 1;
            }

            wordsLeft -= 1;
        }
    }

    return ipsumText;
}

function shuffle(arr) {
    return arr.sort(function () {
        return Math.random() - 0.5
    })
}


function makeIpsum() {
    const numInput = document.getElementById('ipsumLength');
    const numToGenerate = numInput.value;
    if (numToGenerate > 0) {
        const ipsumText = generate(numToGenerate);
        document.getElementById('output').innerText = ipsumText;
    }
}
</script>
