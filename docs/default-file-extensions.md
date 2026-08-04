# Default file extension registration

EmacsCtl reads the comma-separated `fileExtensions` setting from `~/.config/emacsctl/config.json`. In Tony's environment, that path is a symlink to `~/.tony/emacsctl.json`, which is personal configuration and is not committed to this repository.

## Current policy

The configured set contains 144 unique extensions:

```json
{
  "fileExtensions": "h,c,cpp,rs,ts,tsx,js,mjs,cjs,json,yaml,py,swift,java,el,md,sh,toml,yml,go,tex,jsx,gpg,jsonl,ascx,asp,aspx,bash,bash_login,bash_logout,bash_profile,bashrc,bat,bowerrc,c++,cc,cfg,clj,cljs,cljx,clojure,cls,cmake,cmd,code-workspace,coffee,config,containerfile,cs,cshtml,csproj,css,csx,ctp,cxx,dart,diff,dockerfile,dot,dtd,editorconfig,edn,erb,eyaml,eyml,fs,fsi,fsscript,fsx,gemspec,gitattributes,gitconfig,gitignore,gradle,groovy,h++,handlebars,hbs,hh,hpp,hxx,ini,jade,jav,jscsrc,jshintrc,jshtm,jsp,less,lua,m,makefile,markdown,mdoc,mdown,mdtext,mdtxt,mdwn,mk,mkd,mkdn,ml,mli,mm,php,phtml,pl,pl6,pm,pm6,pod,pp,profile,properties,ps1,psd1,psgi,psm1,pug,pyi,r,rb,rhistory,rprofile,rst,rt,sass,scss,sql,t,txt,vb,vue,wxi,wxl,wxs,xaml,xml,zlogin,zlogout,zprofile,zsh,zshenv,zshrc"
}
```

This set starts with the 153 extensions declared by Cursor, removes 12 unsuitable or intentionally browser-owned extensions, then preserves the existing EmacsCtl-only entries `el`, `gpg`, and `jsonl`.

The excluded extensions are:

- Project packages: `xcodeproj`, `xcworkspace`
- Specialized or potentially non-text formats: `ipynb`, `plist`, `svg`
- Generated, data, or potentially large files: `csv`, `lock`, `log`
- Browser content: `htm`, `html`, `shtml`, `xhtml`

## HTML limitation

`htm`, `html`, and `shtml` all resolve to the `public.html` UTI. SwiftDefaultApps 2.0.1 calls LaunchServices to set this handler, but LaunchServices returns `kLSUnknownErr` (`-10810`) for `public.html`. This is a longstanding macOS behavior that also occurs when assigning `public.html` to applications that explicitly declare HTML support.

`xhtml` resolves to the separate `public.xhtml` UTI and can be registered successfully. It is excluded so browser-oriented HTML formats follow one consistent policy.

## Update behavior

EmacsCtl watches the configuration file and re-registers every listed extension after an external edit. Removing an extension stops future registration but does not restore the previous LaunchServices handler. Restore or choose that handler separately when needed.

The current registration code treats the `swda` process exit status as authoritative. SwiftDefaultApps can print an error while still exiting with status zero, so registration diagnostics should also inspect its output or query the resulting handler.
