## 解く前に予想
- Top K Frequent Elementsは上位k個分の最頻値を返す問題

- 最頻値と言えばこれ！というアルゴリズムが浮かんでこないということは、単純に知らないということなので、答えを見る前に知っているArai60で学んできた知識を組み合わせてみる。

  - 線形的にリストを見ていって、値をカウントしていく感じかなあ。セット関数も合わせる気がする。想像するとぼんやりしてる。変に複雑にしそう。
  - 線形的って表現を使ってみた。「順にみていく」の言い換え。

  - ソートした後に連結リストにしてみていくこともできそう。遠回りすぎだな。二回も引数を変更してる。
---
## わからないのでカンニング
- ChatGPT曰く、解法に３つ有り
  - 1.ソート＋ハッシュテーブル
    - そんなに外れていなかったな。うれしい。
    - 知らないセット関数の使われ方してる。怖い。
  - 2.バケットソート
    - 誰ですか？なんですか？
  - 3.最小ヒープ
    - え！？ここでも使えるんですか？？？想像できんのですが
    
---
## 1.ソート＋ハッシュテーブル
- 大いなる誤解 其の一
  - ハッシュテーブルとはPythonではセット関数のことだと思い込んでた。よく見たらCounterというずるいライブラリだった。

- 大いなる誤解 其の二
  - hashsetとhashmapとhashtable？？？？？
  - 私の理解：ハッシュテーブルは値の重複を赦さないデータ構造で、検索とか削除とかが定数時間deできて、それはハッシュ関数のおかげ。
  - 理解の訂正：~~ハッシュテーブル~~ ハッシュセットは値の重複を赦さないデータ構造...

  - ハッシュマップは値の重複は許さないがキーの重複は許すデータ構造。あとはハッシュセットと一緒。

  - ハッシュテーブルはハッシュ関数によってデータの追加検索削除が定数倍で行えるデータ構造

- 大いなる寄り道
  - ハッシュセットとハッシュマップを実装してみるか...ハッシュ関数部分は同じなんだから、そんな合わせてもすごい分量にはならないだろうきっと。
---
## 2.バケットソート＋ハッシュマップ
- お？なんか簡単だ。怖くないじゃないか。
```python3
# 0〜9までのバケツを用意
buckets = [[] for _ in range(10)]

# 数字を対応するバケツに入れる
for n in numbers:
    buckets[n].append(n)
```
- この問題もハッシュマップ使うなら、解法1ソート＋ハッシュマップの兄弟じゃん。ここの題名に「ハッシュマップ」と追記しておこう。
```pyhton3
from collections import Counter

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count = Counter(nums)

        buckets = [[] for _ in range(len(nums) + 1)]

        for num, freq in count.items():
            buckets[freq].append(num)

        result = []

        for freq in range(len(nums), 0, -1):
            for num in buckets[freq]:
                result.append(num)

                if len(result) == k:
                    return result
```
## 3.ヒープ
- なんで最小ヒープ？最頻値を求められるの？
  - ChatGPT曰く、「出現回数が最も少ない数字を簡単に捨てられるからです」

- ここでもハッシュマップか...
```pyhon3
from collections import Counter
import heapq

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count = Counter(nums)
        heap = []

        for num, freq in count.items():
            heapq.heappush(heap, (freq, num))

            if len(heap) > k:
                heapq.heappop(heap)

        return [num for freq, num in heap]
```
## 寄り道：ハッシュセットとハッシュテーブルを実装してみよう。
