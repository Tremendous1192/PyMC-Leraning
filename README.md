# PyMC-Leraning
[PyMC project website](https://www.pymc.io/welcome.html)の学習リポジトリ.

コード説明などは,DeepLやGoogle翻訳で翻訳しました.

1. Introductory
    1. [General Overview](https://www.pymc.io/projects/docs/en/stable/learn/core_notebooks/pymc_overview.html)
    1. [Simple Linear Regression](https://www.pymc.io/projects/docs/en/stable/learn/core_notebooks/GLM_linear.html)
        * 単回帰分析のチュートリアル.```bambi```を用いると```stastmodels```に似た回帰式の定義ができる.
    1. [General API quickstart](https://www.pymc.io/projects/docs/en/stable/learn/core_notebooks/GLM_linear.html)
        * ```pm.MutableData```を用いると[交差検証](https://www.pymc.io/projects/examples/en/latest/introductory/api_quickstart.html#predicting-on-hold-out-data)ができる.
1. Library Fundamentals
    1. [Distribution Dimensionality](https://www.pymc.io/projects/docs/en/stable/learn/core_notebooks/dimensionality.html)
        * ```pm.model_to_graphviz(モデル)```を用いるとモデル構造を可視化できる.
    1. [PyMC and PyTensor](https://www.pymc.io/projects/docs/en/stable/learn/core_notebooks/pymc_pytensor.html)
1. How to
    1. [Prior and Posterior Predictive Checks](https://www.pymc.io/projects/docs/en/stable/learn/core_notebooks/posterior_predictive.html)
        * 単回帰分析$y = a + bx$とロジスティック回帰分析を例に,事前分布・事後分布・予測値分布を確認する方法を紹介している.
        * ```RANDOM_SEED = 58```, ```rng = np.random.default_rng(RANDOM_SEED)```でノートブック全体の乱数を固定している.
        * サンプラーの予測精度を保つために,目的変数と説明変数を**両方とも標準化**している.
        * 事前分布
            * パラメータ推定値: ```idata = pm.sample_prior_predictive(samples = 50, random_seed = rng)```
            * 予測値計算: ```x = xarray.DataArray(np.linspace(-2, 2, 50), dims = ["plot_dim"])```, ```y = idata.prior["a"] + idata.prior["b"] * x```
            * 予測値プロット: ```ax.plot(x, y.stack(sample = ("chain", "draw")), c = "k", alpha = 0.4)```
        * 事後分布
            * 事後分布計算: ```idata.extend(pm.sample(1000, tune = 2000, random_seed = rng))```
            * ```az.plot_trace(idata)```でパラメータ推定値のばらつきを可視化して,サンプリングが成功したか否かを確認できる.
            * ```pm.sample_posterior_predictive(idata, extend_inferencedata = True, random_seed = rng)```で事後分布によるパラメータ推定値を取得する.
            * ```az.plot_ppc(idata, num_pp_samples = 100)```は予測値のばらつきが,構築したモデル通りだったか否かを視覚的に確認する関数.
        * 予測
            * ```mu_pp = idata.posterior["a"] + idata.posterior["b"] * xarray.DataArray(predictor_scaled, dims = ["obs_id"])```で予測値を計算する.
            * 予測曲線プロット: ```plt.plot( predictor_scaled, mu_pp.mean(("chain", "draw")), label="Mean outcome", color = "C1", alpha = 0.6 )```
            * 観測値プロット: ```plt.scatter(predictor_scaled, idata.observed_data["obs"])```
            * 予測範囲プロット: ```az.plot_hdi(predictor_scaled, idata.posterior_predictive["obs"])```
    1. [Model Comparison](https://www.pymc.io/projects/docs/en/stable/learn/core_notebooks/model_comparison.html)
        * 複数のモデルの精度を比較する方法が書かれている.
    1. [Updating priors](https://www.pymc.io/projects/examples/en/latest/howto/updating_priors.html)
        * PyMC3で書かれているので省略する
    1. [Automatic marginalization of discrete variables](https://www.pymc.io/projects/examples/en/latest/howto/marginalizing-models.html)
        * 開発中の機能を使用するようなので,数式を写経して終わりにする.
    1. [How to debug a model](https://www.pymc.io/projects/examples/en/latest/howto/howto_debugging.html)
        * ```Print```や```eval```を用いたデバッグ方法について紹介している.
    1. [How to wrap a JAX function for use in PyMC](https://www.pymc.io/projects/examples/en/latest/howto/wrapping_jax_function.html)
        * ```jax```, ```numpyro```のインストールが必要とのことでパス.
    1. [Splines](https://www.pymc.io/projects/examples/en/latest/howto/spline.html)
        * 桜の開花時期をお題にして,スプライン曲線の作成方法を紹介しています.
    1. [Bayesian copula estimation: Describing correlated joint distributions](https://www.pymc.io/projects/examples/en/latest/howto/copula-estimation.html)
        * 複雑な確率分布を簡単な確率分布の組合せで表現しようとする方法を紹介しています




# 実験
1. 20240403 3次関数の回帰分析
    * 3次関数の近似をベイズ推論で行った.
1. 20240404 3次関数の回帰分析(スケーリング無し)
    * 前日の関数近似を変数のスケーリング無しで行った.この程度の複雑さの場合,スケーリングがなくとも十分に予測できるようだ.
1. 20240404 3次関数の回帰分析(pandas, DataFrameに定数項含)
    * Pandasデータフレームを用いてモデル作成と予測を実施した.
    * メモリの無駄遣いだが,データフレームに定数項を含めた
    * モデル作成時はPyTensorで書くとエラーが発生しにくい.
1. 20240404 3次関数の回帰分析(pandas, DataFrameに定数項含めず)
    * **Pandasデータフレームを用いてモデル作成と予測を実施した.**
    * Pnadasデータフレームに定数項を含めずとも予測できるようにした.


# 引用
* [PyMC公式サイト](https://www.pymc.io/welcome.html)
* [データセット](https://github.com/pymc-devs/pymc-examples)

# 参考書籍
* [Pythonでスラスラわかる　ベイズ推論「超」入門 | 書籍情報 | 株式会社 講談社サイエンティフィク](https://www.kspub.co.jp/book/detail/5337639.html)

# 参考URL
* [Markdownで数式をそろえるメモ（少しだけ） | 寝坊した](https://oversleptabit.com/archives/5155)
* [Markdown：数式の書体 | 寝坊した](https://oversleptabit.com/archives/5397)
* [Markdown：数式の場合分け | 寝坊した](https://oversleptabit.com/archives/5391)
* [PyMC：重回帰を題材にしたPyMCの紹介](https://zenn.dev/yoshida0312/articles/bbd246d3da42b3#%E4%BA%88%E6%B8%AC)
* [はてなブログMarkdownでTex数式を書くコツとチートシート - ari23の研究ノート](https://ari23ant.com/entry/hatenatex-markdown)
* [LaTeXコマンド - 数学記号（等号、不等号、演算子、集合）](https://medemanabu.net/latex/operators/)
* [【TeX】数学の『式』をVSCodeで簡単・キレイに表示する #VSCode - Qiita](https://qiita.com/fluffyOkitsune/items/94e0818e8d8c2acc0db1)
* [LaTeXコマンド - ギリシャ文字](https://medemanabu.net/latex/greek/)
* [Qiitaの数式チートシート - Qiita](https://qiita.com/PlanetMeron/items/63ac58898541cbe81ada)

