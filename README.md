## homebrew on ppc64le support

Is it possible to port homebrew to Linux ppc64le? The answer is yes.

## How do I build the portable ruby that homebrew use?

Firstly, I manually built my portable ruby version based on portable-ruby formula in https://github.com/Homebrew/homebrew-portable-ruby. Basically I create a bash script that replicate exact way that homebrew build that formula.

Once I have a temporary binary that I could play with, I patched `Library/Homebrew/cmd/vendor-install.sh`:

```
diff --git a/Library/Homebrew/cmd/vendor-install.sh b/Library/Homebrew/cmd/vendor-install.sh
index fdd491362..1a37db38c 100644
--- a/Library/Homebrew/cmd/vendor-install.sh
+++ b/Library/Homebrew/cmd/vendor-install.sh
@@ -39,6 +39,11 @@ then
       ruby_URL2="https://github.com/Homebrew/homebrew-portable-ruby/releases/download/2.6.3/portable-ruby-2.6.3.armv6_linux.bottle.tar.gz"
       ruby_SHA="78e36e4671fd08790bfbfda4408d559341c9872bf48a4f6eab78157a3bf3efa6"
       ;;
+    ppc64le)
+      ruby_URL="https://raw.githubusercontent.com/runlevel5/linuxbrew-ppc64le/master/portable-ruby-2.6.3.ppc64le_linux.manually_compiled.tar.gz"
+      ;;
   esac
 fi
```

Next I try to get `brew` up and running so I could build the final portable ruby version:

```
brew install --build-bottle homebrew/portable-ruby/portable-ruby
brew bottle homebrew/portable-ruby/portable-ruby
```

The end result is the final binary `portable-ruby-2.6.3.ppc64le_linux.bottle.tar.gz`

In order to get `brew` working under ppc, please applying following patches accordingly:

```
git clone https://github.com/Homebrew/brew.git
cd brew && wget https://raw.githubusercontent.com/runlevel5/linuxbrew-ppc64le/master/patches/brew/0001-support-ppc64le.patch
git am 0001-Support-ppc64le.patch

git clone https://github.com/Homebrew/linuxbrew-core.git
wget https://raw.githubusercontent.com/runlevel5/linuxbrew-ppc64le/master/patches/linuxbrew-core/0001-support-ppc64le.patch
cd linuxbrew-core && git am 0001-Support-ppc64le.patch

git clone https://github.com/Homebrew/homebrew-portable-ruby.git
cd homebrew-portable-ruby && git am 0001-update-makedepend-with-patched-version.patch
```
