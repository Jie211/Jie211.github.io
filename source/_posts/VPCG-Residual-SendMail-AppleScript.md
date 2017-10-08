---
title: VPCG Residual & SendMail AppleScript
tags:
  - applescript
  - mail
categories:
  - lab
date: 2014-11-14 00:00:00
---

VPCG_CR法がうまく収束しないみたいから，最大反復回数と収束判定子と収束の関係を調べました．
<!-- more -->
bcsstk17とbcsstk14の行列を用いて実験した，大体収束判定子は1.0e-3以上であれば収束する，同じ収束判定子で反復回数が300の時は反復回数が100の時より外部反復が2倍以上減りました．


    #!applescript
    set aSubject to "完成した～～～！"
    set theSender to "YYY@gmail.com" --送信者アドレス
    set recipientAddr to "XXX@me.com" --受信者アドレス
    set bodyText to "任務完成しました！！！チェックしてください！！！"

    sendMail(aSubject, theSender, recipientAddr, bodyText) of me

    --Mail.appでメール送信を行う
    on sendMail(theSubject, theSender, recipientAddr, bodyText)
      tell application "Mail"
        set newMessage to make new outgoing message with properties {subject:theSubject, content:bodyText}
        tell newMessage
          set visible to true
          set sender to theSender
          make new to recipient at end of to recipients with properties {address:recipientAddr}
          send
        end tell
      end tell
    end sendMail
