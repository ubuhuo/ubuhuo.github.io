---
title: Sublime Text 3 安装 SVN 插件
date: 2017/8/10
categories:
  - 软件使用
  - SublimeText
tags:
  - Sublime Text 3 安装 SVN 插件
copyright: true
abbrlink: 59657ebc
---

## 1. 第一步

若你是第一次使用 `Sublime Text` 需要先安装插件管理器方法如下：

首先通过 `ctrl + ~` 唤出控制台

如果你是 `Sublime Text 3` 将下面代码复制到窗口并按回车键运行

```
import urllib.request,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

如果你是 `Sublime Text 2` 将下面代码复制到窗口并按回车键运行

```
import urllib2,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```

## 2. 第二步

等待下载完成之后按组合键 `CTRL + SHIFP + P` 唤出命令窗口并在窗口输入 `install` 选择 `install Package` 回车，然后你会看见左下角有个 `=` 号在一直晃动,等待一下就会出来可以安装的包,此时你只需要输入 `svn`

如果你的是 `Windows` 的则选择(需要提前安装 `TortoiseSVN`，也就是我们说的小乌龟)

如果是 `Linux` 的则选择 `SVN`

此时左下角显示 `Installing package TortoiseSVN` 或者 `Installing package SVN`，下载完成之后会自动打开一个文件。

你要是默认安装的 `TortoiseSVN`，则不需要更改直接可以使用，要是自定义的路径，则需要手动配置 `Sublime Text` 的 `SVN` 插件路径，具体的配置文件在 `Preferences`（首选项）-> `Package Settings` -> `TortoiseSVN` -> `Settings` -> `User`

如我的路径是 `F:\TortoiseSvn\bin\TortoiseProc.exe`

则在刚才打开的配置文件里边输入一下内容：

```json
{
    // Auto close update dialog when no errors, conflicts and merges
    "autoCloseUpdateDialog": false,
    "tortoiseproc_path": "D:\\SVN\\bin\\TortoiseProc.exe"
}
```

注意，目录层之间用 `\\\` 分割而不是 `\`

现在你在你左侧栏中右键就能看见 `SVN` 的操作选项了