Github利用テストのメモ(Windows)
===============================

## mysysGitの導入
[Git/一人で使ってみる（msysgit編）](http://yakinikunotare.boo.jp/orebase/index.php?Git%2F%B0%EC%BF%CD%A4%C7%BB%C8%A4%C3%A4%C6%A4%DF%A4%EB%A1%CAmsysgit%CA%D4%A1%CB)を参考にすれば基本的に問題ない。

基本的にインストールはネクスト連打でOK。ぼくは、「エクスプローラーにコマンド追加」と「コマンドラインのTrueTypeフォントうんたら」のチェックは外した。

## 各種ファイルへの追記。
各種設定ファイルに項目を追記して使いやすくする。

### .gitconfig
場所はユーザーフォルダ直下。

	[core]
		editor = D:/OnlineSoft/TeraPad/TeraPad.exe
		autocrlf = true
		pager = nkf -s | LESSCHARSET=utf-8 less
		safecrlf = true
	[alias]
		co = checkout
		ci = commit
		st = status
		br = branch
		hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
		type = cat-file -t
		dump = cat-file -p

### profile
場所はインストールフォルダ/etc直下。

	export GIT_PAGER="nkf -s | less"
	alias ls='ls --show-control-chars'
	
	# http://library.edgecase.com/git_immersion/lab_11.html
	alias gs='git status '
	alias ga='git add '
	alias gb='git branch '
	alias gc='git commit'
	alias gd='git diff'
	alias go='git checkout '
	alias gk='gitk --all&'
	alias gx='gitx --all'
	
	alias got='git '
	alias get='git '

最後に上記を追記する。

続いて同ファイルに`. /etc/git-completion.bash`という行が少し上にあるので、その少し下の一文を変更する。

		PS1='\[\033]0;$MSYSTEM:\w\007
	\033[32m\]\u@\h \[\033[33m\w$(__git_ps1)\033[31m\]$(__git_not_pushed)\033[0m\]
	$ '

これは、[pushし忘れないようにプロンプトに表示するようにした](http://d.hatena.ne.jp/pasela/20110216/git_not_pushed)への対応の一部。

### git-completion.bash
場所はインストールフォルダ/etc直下。

先程の、[pushし忘れないようにプロンプトに表示するようにした](http://d.hatena.ne.jp/pasela/20110216/git_not_pushed)での関数を追記する。末尾でいい。

	__git_not_pushed()
	{
		if [ "$(git rev-parse --is-inside-work-tree 2>/dev/null)" = "true" ]; then
			head="$(git rev-parse HEAD)"
			for x in $(git rev-parse --remotes)
			do
				if [ "$head" = "$x" ]; then
					return 0
				fi
			done
			echo " NOT PUSHED"
		fi
		return 0
	}

### inputrc
場所はインストールフォルダ/etc直下。

`# disable/enable 8bit input`と記された項目を設定する。

	set input-meta on
	set convert-meta off
	set meta-flag on
	set output-meta on
	set kanji-code utf-8

## 諸注意
コミット時のエディタに[TeraPad](http://www5f.biglobe.ne.jp/~t-susumu/library/tpad.html)を指定している。起動時の引数に`/cu8n`を与えればUTF-8Nで起動できるが、上手くいかなかった。パスを通し`TeraPad /cu8n`とすればいいかも知れない。

TeraPad側の設定で読み込みに「文字コードを自動判別しない」とし、起動時・保存時共にUTF-8Nを指定したが、コミット時にはShift_Jisとして起動してしまう。「文字コードを指定して読み直す」か2度上書き保存すればいい。1度の保存でもUTF-8Nで保存されるはずだが、うまくいかない。

____________________________

標準ではGit BashはLucida Unicodeで表示され、日本語が表示できない。Git Bashを起動するショートカットを管理者権限で起動しプロパティからフォントをラスターフォントへ変更すればいい。

しかし、コミットする度に警告をうける。直し方は知らない。気にしない。
