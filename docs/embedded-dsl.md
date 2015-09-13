##Free Monad
* [The free package](http://hackage.haskell.org/package/free)

```haskell
data Free f a = Pure a | Free (f (Free f a))

instance Functor f => Monad (Free f) where
  return = Pure
  Free x >>= f = Free (fmap (>>= f) x)
  Pure x >>= f = f x
```

* [Why free monads matter](http://www.haskellforall.com/2012/06/you-could-have-invented-free-monads.html)
* [Freeモナドって何なのさっ！？](http://d.hatena.ne.jp/its_out_of_tune/20121111/1352632815)
* [そろそろFreeモナドに関して一言いっとくか](http://d.hatena.ne.jp/fumiexcel/20121111/1352614885)
* [Asymptotic Improvement of Computations over Free Monads](http://www.janis-voigtlaender.eu/papers/AsymptoticImprovementOfComputationsOverFreeMonads.pdf)
* [Reflection without Remorse](http://homepages.cwi.nl/~ploeg/papers/zseq.pdf)
  * [Haskell 2014: Reflection without Remorse: Revealing a hidden sequence to speed up monadic reflection](https://www.youtube.com/watch?v=_XoI65Rxmss)
* [自由モノイドとFreeモナド](http://d.hatena.ne.jp/hiratara/20130525/1369495124)
* [Free monads are free](http://mbps.hatenablog.com/entry/2014/07/08/201210)
* [Free monad](http://mbps.hatenablog.com/entry/2014/06/19/190518)
* [Category of monads](http://mbps.hatenablog.com/entry/2014/06/26/123223)
* [Free Monads for Less (Part 2 of 3): Yoneda](http://comonad.com/reader/2011/free-monads-for-less-2/)
* [What is the difference between free monads and free monoids?](http://www.reddit.com/r/haskell/comments/2znhjk/what_is_the_difference_between_free_monads_and/)
* [Interpreting Free Monads of Functor Sums](https://gist.github.com/avieth/334201aa341d9a00c7fc)
* [Examining Hackage: operational](http://jozefg.bitbucket.org/posts/2014-12-27-folds.html)
* [Free for DSLs, cofree for interpreters](http://dlaing.org/cofun/posts/free_and_cofree.html)
* [Reasoning about Errors in Free Monads and Their Interpreters](http://aaronlevin.ca/post/122079863187/reasoning-about-errors-in-free-monads-and-their)
* [Free Monads from Functors from GADTs](http://jstimpfle.de/fun/free-monads-gadts.html)
* [Functional Pearl: A Smart View on Datatypes](http://www.fceia.unr.edu.ar/~mauro/pubs/smartviews/smartviews.pdf)
* [これが、唯一まともなコルーチン実装法 98%のHaskellerが知らないモナドの常識【初学者要注意！】](http://qiita.com/hiyakashi_/items/1c1ec5445e7586a2fa06)
* [FreeモナドとTagless FinalによるDependency InjectionのためのDSL](http://qiita.com/yyu/items/377513f17fec536b562e)
* [Freeモナドで領域特化言語を作るとプリティミューテーション](http://qiita.com/hiruberuto/items/3d55b0e54565dbb286a7)
* [Freer Monads, More Extensible Effects](http://okmij.org/ftp/Haskell/extensible/more.pdf)

###Coyoneda
* [Loop School - Coyoneda](http://school.looprecur.com/?video=122708005)

```haskell
data Coyoneda f a where
    Coyoneda :: (b -> a) -> (f b) -> Coyoneda f a

instance Functor (Coyoneda f) where
    fmap f (Coyoneda g fb) = Coyoneda (f . g) fb
```

抽象データ型からファンクタを作ることができる。Freeと組み合わせれば手軽にモナド(DSL)が手に入る。

```haskell
data Proc a = Action1 a | Action2 a | Action3 a

act1 = Free $ Coyoneda id (Action1 (Pure ()))
act2 = Free $ Coyoneda id (Action2 (Pure ()))
act3 = Free $ Coyoneda id (Action3 (Pure ()))

runProc :: Free (Coyoneda Proc) () -> IO ()
runProc (Pure ()) = putStrLn "end"
runProc (Free (Coyoneda f act)) = case act of
    Action1 a -> putStrLn "act1" >> runProc (f a)
    Action2 a -> putStrLn "act2" >> runProc (f a)
    Action3 a -> putStrLn "act3" >> runProc (f a)

proc :: Free (Coyoneda Proc) ()
proc = do
    act1
    act2
    act3

main = runProc proc
```

* [Step by Step / Deep explain: The Power of (Co)Yoneda (preferably in scala) through Coroutines](http://stackoverflow.com/questions/24000465/step-by-step-deep-explain-the-power-of-coyoneda-preferably-in-scala-throu#24006085)
* [Yoneda principle](http://mbps.hatenablog.com/entry/2015/02/28/055025)
* [Co-Yoneda lemma](http://mbps.hatenablog.com/entry/2014/04/05/002411)
* [EndによるYoneda lemma](http://mbps.hatenablog.com/entry/2014/04/04/044134)
* [(Co)Yoneda reduction for free](http://mbps.hatenablog.com/entry/2014/04/12/074615)
* [Co-Yoneda lemma in colimits](http://mbps.hatenablog.com/entry/2014/09/30/150836)

```haskell
newtype Natural f g = Natural (forall x. f x -> g x)

yoneda :: Functor f => f a -> Natural ((->) a) f
yoneda f = Natural $ \ax -> fmap ax f
```

<https://twitter.com/fumieval/status/576349394724765697>

###Operational Monad
* [operational](https://hackage.haskell.org/package/operational)
* [Freeモナドを超えた！？operationalモナドを使ってみよう](http://fumieval.hatenablog.com/entry/2013/05/09/223604)
* [Operational monad](http://mbps.hatenablog.com/entry/2014/07/10/010000)
* [09. Operationalモナド](https://sites.google.com/site/toriaezuzakki/haskell/operational)
* [Yoneda lemmaとOperational Monad](http://myuon-myon.hatenablog.com/entry/2013/06/09/135407)

##Ideal Monad
* [究極のモナド「Idealモナド」を垣間見る](http://fumieval.hatenablog.com/entry/2013/01/04/110339)
* [究極のモナド「Idealモナド」を垣間見る(続/その0)](http://fumieval.hatenablog.com/entry/2013/01/07/113221)
* [What is the correct definition of ideal monads?](http://theorylunch.wordpress.com/2012/11/22/what-is-the-correct-definition-of-ideal-monads/)

##The Expression Problem
* [The Expression Problem](http://maoe.hatenadiary.jp/entry/20101214/1292337923)
* [Data types à la carte](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.101.4131)
* [C9 Lectures: Dr. Ralf Lämmel - Advanced Functional Programming - The Expression Problem](https://channel9.msdn.com/Shows/Going+Deep/C9-Lectures-Dr-Ralf-Laemmel-Advanced-Functional-Programming-The-Expression-Problem)

##Tagless Final
* [Typed Tagless Final Interpreters](http://okmij.org/ftp/tagless-final/course/lecture.pdf)
* [Typed tagless-final interpretations: Lecture notes](http://okmij.org/ftp/tagless-final/course/)
* [Typed Tagless-Final Linear Lambda Calculus](https://www.fpcomplete.com/user/mutjida/typed-tagless-final-linear-lambda-calculus)