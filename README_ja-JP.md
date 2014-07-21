# NYTimes Objective-Cスタイルガイド

このスタイルガイドは、NYTimes iOSチームのコーディング慣習を記述しています。フィードバックは、[issue](https://github.com/NYTimes/objetive-c-style-guide/issues)、[プルリクエスト](https://github.com/NYTimes/objetive-c-style-guide/pulls)、[ツイート](https://twitter.com/nytimesmobile)でお願いします。それと、[積極採用中](http://jobs.nytco.com/job/New-York-iOS-Developer-Job-NY/2572221/)です。

全ての[コントリビューター](https://github.com/NYTimes/objective-c-style-guide/contributors)に感謝します。

## はじめに

下記にスタイルガイドに有用なアップル公式ドキュメントをあげます。本ドキュメントで述べられていないことがあれば、下記ドキュメントのいずれかに詳細に記述されているかもしれません。

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## 索引

* [ドット記法](#ドット記法)
* [スペース](#スペース)
* [条件分岐](#条件分岐)
  * [三項演算子](#三項演算子)
* [エラー制御](#エラー制御)
* [メソッド](#メソッド)
* [変数](#変数)
* [命名](#命名)
* [コメント](#コメント)
* [initとdealloc](#initとdealloc)
* [リテラル](#リテラル)
* [CGRect関数](#CGRect関数)
* [定数](#定数)
* [列挙型](#列挙型)
* [ビットマスク](#ビットマスク)
* [プライベートプロパティ](#プライベートプロパティ)
* [画像名](#画像名)
* [真偽値](#真偽値)
* [シングルトン](#シングルトン)
* [Xcodeプロジェクト](#Xcodeプロジェクト)

## ドット記法

ドット記法は、**常に**プロパティの読み込みと書き込みの時のみ使用しましょう。ブラケット記法（[）は、その他全ての場合に使用できます。

**良い例:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**悪い例:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## スペース

* スペース4つでのインデントを行ってください。タブでのインデントは避けてください。この設定はXcodeにしておきましょう。
* メソッドや`if`/`else`/`switch`/`while`等のブレース（{）は、常に命令文と同じ行に開き括弧を書き、新しい行に閉じ括弧を書いてください。

**良い例:**
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```
* メソッド定義の間には、秩序立ち読みやすくするために1行だけ空行を入れましょう。
* メソッド内のスペース等は機能的な分割を示しますが、それが必要な場合は時に新しいメソッドを作るべきかもしれません。
* `@synthesize`と`@dynamic`は、それぞれ実装内の新しい行に定義しましょう。

## 条件分岐
条件分岐では、ミスを防ぐため、文法上必要ない場合（括弧内が1文の場合）でも常にブレース（{）を使いましょう。そうしなければ、2行目を足した時に条件分岐内であると勘違いする[ミス](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256)が起こりえます。また、条件分岐内の文がコメントアウトされた時に、その次の行が図らずも条件分岐内に含まれてしまうという[危険な欠陥](http://programmers.stackexchange.com/a/16530)を発生させるかもしれません。また、このスタイルは他の全ての条件式でも一貫しているので、簡単にスキャンできます。

**良い例:**
```objc
if (!error) {
    return success;
}
```

**悪い例:**
```objc
if (!error)
    return success;
```

もしくは

```objc
if (!error) return success;
```

### 三項演算子

三項演算子は、明確さときれいさが増す時にだけ使用してください。三項演算子内で評価するのは、単一の条件式のみにしてください。複数の条件式が必要な場合は、一般的に理解しやすいif文を使うか、変数に代入してください。

**良い例:**
```objc
result = a > b ? x : y;
```

**悪い例:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## エラー制御

メソッドが参照渡しの引数でエラーを返す場合、エラー制御に入る判断には、引数ではなく戻り値を使用してください。

**良い例:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**悪い例:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

いくつかのアップルのAPIは、処理成功時に参照渡し引数に未定義の値（NULLでない値）を書き込むため、エラーの検出漏れとそれによるクラッシュを引き起こす可能性があります。

## メソッド

メソッドの表記では、スコープ（-や+）の後にスペースを入れてください。メソッドの構成要素の間には、スペースを1つ入れましょう。

**良い例**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
## 変数

変数には、可能な限り記述的な名前を付けてください。1文字の変数名は、`for()`ループ内以外では避けましょう。

ポインタであることを示すアスタリスクは、変数名の前に置いてください。すなわち、`NSString* text`や`NSString * text`ではなく、`NSString *text`です。ただし、定数の場合は除きます。

裸のインスタンス変数は可能な限り使わず、プロパティ定義を行ってください。インスタンス変数への直接アクセスは、初期化メソッド（`init`、`initWithCoder:`等）や`dealloc`メソッド、カスタムセッターやゲッターメソッド以外では避けてください。初期化メソッドや`dealloc`でアクセサメソッドを使う場合の詳細な情報については、[こちら](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6)を参照してください。

**良い例:**

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**悪い例:**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

#### 変数修飾子

[ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4)の変数修飾子（`__strong`、`__weak`、`__unsafe_unretained`、`__autoreleasing`）は、アスタリスクと変数名の間に置いてください。すなわち、`NSString * __weak text`のようにしてください。

## 命名

アップルの命名慣習には、可能な限り従ってください。特に、[メモリ管理規則](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html)（[NARC](http://stackoverflow.com/a/2865194/340508)）に関するものは忠実に従ってください。

長く記述的なメソッド・変数名を付けるようにしましょう。

**良い例:**

```objc
UIButton *settingsButton;
```

**悪い例**

```objc
UIButton *setBut;
```
3文字のプレフィックス（`NYT`）をいつもクラス名や定数名に使用してください。ただし、CoreDataのエンティティ名についてはその限りではありません。定数名は、全単語大文字で始め、関連するクラス名をプレフィックスとしたキャメルケースにしてください。

**良い例:**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**悪い例:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

プロパティ名とローカル変数名は、先頭の単語だけ小文字で始めるキャメルケースにしてください。
インスタンス変数は、先頭の単語だけ小文字で始めるキャメルケースで、アンダースコアをプレフィックスにしてください。これは、LLVMにより自動的にsynthesizeされて生成される変数と一貫したルールです。**ただし、LLVMが自動的に変数をsynthesizeできる場合はそうしてください。**

**良い例:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**悪い例:**

```objc
id varnm;
```

## コメント

必要な場合は、コメントで**なぜ**そのコードが書かれているのかを記述してください。コード中の全てのコメントは、最新に保たれているか削除しなければなりません。

コードは可能な限りコード自身が記述的であるべきで、断続的な少ない行の説明しか必要ないはずなので、ブロックコメントは全般的に避けましょう。ただし、このルールは、ドキュメントの自動生成に必要な場合には適用されません。

## initとdealloc

`dealloc`メソッドは、`@synthesize`と`@dynamic`の直後、実装の最初においてください。`init`は、`dealloc`メソッドの直下に置いてください。

`init`メソッドは、下記のように構成しましょう:

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initalizer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

## リテラル

`NSString`、`NSDictionary`、`NSArray`、`NSNumber`のリテラルは、それらの変更できないインスタンスを作る際には常に利用してください。`NSArray`や`NSDictionary`に`nil`を渡してしまうとクラッシュするので注意しましょう。

**良い例:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**悪い例:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect関数

`CGRect`の`x`、`y`、`width`、`height`にアクセスする際には、直接メンバー変数にアクセスするのではなく、常に`CGGeometry`の関数を使いましょう。アップルの`CGGeometry`リファレンスには、下記の記述があります。

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**良い例:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**悪い例:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## 定数

共通して利用されている変数の再利用を簡単にしたり、検索と置換なしに素早い変更を行うため、コード中の文字列リテラルや数値リテラルは定数化しましょう。定数は`static`で定義します。`#define`は、明確にマクロとして利用される場合以外は使わないでください。

**良い例:**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**悪い例:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## 列挙型

enumを使用する時は、強い型チェックとコード補完機能を利用するため、新しい型定義仕様（`NS_ENUM()`）を使うようにしましょう。現在、SDKには新しい型定義の使用を促進するマクロが組み込まれています。

**良い例:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## ビットマスク

ビットマスクを使う時は、`NS_OPTIONS`マクロを使ってください。

**良い例:**

```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
  NYTAdCategoryAutos      = 1 << 0,
  NYTAdCategoryJobs       = 1 << 1,
  NYTAdCategoryRealState  = 1 << 2,
  NYTAdCategoryTechnology = 1 << 3
};
```

## プライベートプロパティ

プライベートプロパティは、実装ファイルのクラス拡張（無名カテゴリ）で定義しましょう。名前付きカテゴリ（`NYPrivate`や`private`等）は、他のクラスを拡張するのでない限り使わないようにしましょう。

**良い例:**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## 画像名

開発者の混乱を防ぐため、画像には一貫した名前を付けましょう。画像名は、画像の目的、（存在する場合）画像に関連するクラスやプロパティのプレフィックスを省略した名前、画像の色や配置等のより詳細な説明、画像が使われる状態、を順に記述したキャメルケース文字列にしてください。

**良い例:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

似た目的で使用される画像は、関連画像をまとめた画像フォルダにグループ化しましょう。

## 真偽値

`nil`は`NO`と判定されるため、条件式内で比較を行う必要はありません。`YES`は1と定義されていて、`BOOL`は8ビットまでしか保証しないので何かを直接`YES`と比較することはやめてください。

これは、コードの一貫性や可読性を上げるためでもあります。

**良い例:**

```objc
if (!someObject) {
}
```

**悪い例:**

```objc
if (someObject == nil) {
}
```

-----

**`BOOL`についての2つの良い例:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**悪い例:**

```objc
if (isAwesome == YES) // Never do this.
if ([someObject boolValue] == NO)
```

-----

`BOOL`プロパティの名前が形容詞的である場合、プロパティ名には"is"プレフィックスを付けず、ゲットアクセサに慣習的な名前を指定してください。

**良い例:**
```objc
@property (assign, getter=isEditable) BOOL editable;
```
本文と例は、[Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)からとられたものです。

## シングルトン
シングルトンオブジェクトでは、共有インスタンスの作成にスレッドセーフパターンを用いましょう。
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```

これは、[確率的に発生し、時に頻度高く起きるクラッシュを防ぐことができます](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html)。

## Xcodeプロジェクト

物理ファイルは、ファイルが散らかるのを防ぐためにXcopeプロジェクトファイルと同期しておいてください。全てのXcodeのファイルグループを、ファイルシステムのフォルダに反映しましょう。コードはファイルの種類だけでなく、特徴でグループ化してわかりやすくしましょう。

可能であれば、常にターゲットビルド設定で"Treat Warning as Errors"オプションと、可能な限り多くの[追加警告](http://boredzo.org/blog/archives/2009-11-07/warnings)を有効にしましょう。もし特定の警告を無視する必要がある場合は、[Clangのプラグマ機能](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html)を使ってください。

# 他のObjective-Cスタイルガイド

もし、本コーディングスタイルがあなたがたのテイストに合わない場合は、下記のその他のスタイルガイドを見てみてください。

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
