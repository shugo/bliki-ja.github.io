---
title: 最適化はリファクタリングか？
tags: TAGS
---

http://martinfowler.com/bliki/IsOptimizationRefactoring.html

[[RefactoringBoundary]]のひとつ

'''プログラムのパフォーマンスを向上させるために変更を行った。これはリファクタリングと呼べるのか？'''

最適化とリファクタリングはどちらも変化を伴うものだが（なかには最適化かつリファクタリングとなる変化もあるが）、両者は別物だと私は考えている。

なぜなら、両者の目的が異なるからである。リファクタリングは、コードを理解しやすくするためである。最適化は、プログラムを速くするためである。変数の展開はどちらの場合にも用いられる。リファクタリングを行っているなら、それはコードをきれいにするためだろう。成功したかどうかの判断は、その変更によってプログラムが理解しやすくなったかというあなたの（主観的な）評価によって決まる。最適化を行っているなら、パフォーマンスを向上させるためだろう。変更の前後でプロファイラを使い、行った最適化がパフォーマンス向上につながっているか確認するとよい。パフォーマンスが重要となる状況であれば、環境（コンパイラ、VM等）が変わるときのために変更履歴を保存しておき、再テストしてその効果を確かめるとよい。

つまり、リファクタリングと最適化は似ており、共通する変更がありはするものの、目的が異なるため、両者は別物であると私は考えている。