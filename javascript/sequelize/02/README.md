# sequelize - mySQL ORM

## CRUD 사용하기

```
// 글쓰기
router.post('/', isLoggedIn, async (req, res, next) => {
	try{
		const newPost = await db.Post.create({
			auth: req.body.auth,
			nickname: req.body.nickname,
			href: req.body.href || null,
			title: req.body.title,
			avatar: req.body.avatar || null,
			description: req.body.description,
			content: req.body.content,
			imagePath: req.body.imagePath || null,
			category: req.body.category,
			UserId: req.user.id,
		});
		console.log('뉴포스트 = ', newPost);
		return res.status(200).json(newPost);
	}catch(e){
		console.error(e);
		next(e);
	}
});
```
레코드를 만들려면 정의한 모델의 create() 메소드를 사용하면 된다.

```
// 글목록 조회
router.post('/find', async (req, res, next) => {
	try{
		const posts = await db.Post.findAll({
			where: { category: req.body.category },
			attributes: ['id','auth','nickname','href','title','avatar','description','content','imagePath','category', 'star','createdAt','updatedAt'],
			order: [['createdAt', 'DESC']],
			include: [{
				model: db.User,
				as: 'Likers',
			}],
		})
		return res.json(posts);
	}catch(e){
		console.error(e);
		next(e);
	}
});
```
레코드를 조회하기 위해서는 정의한 모델의 findAll() 메소드를 사용하면 된다.
V3에서는 find()를 통해 조회했으나 findAll()로 변경되었다고 알고 있다.

```
// 싱글포스트 조회
router.get('/:id', async (req, res, next) => {
	try{
		const post = await db.Post.findOne({
			where: { id: req.params.id, },
			include: [{
				model: db.User,
				through: 'Like',
				as: 'Likers',
				attributes: ['id'],
			}]
		});
		console.log('포스트', post);
		return res.status(200).json(post);
	}catch(e){
		console.error(e);
		next(e);
	}
});
```
한개의 결과물을 얻고싶을땐 findOne()을 사용한다.

```
// 정보 수정

router.patch('/nickname', isLoggedIn, async (req, res, next) => {
	try{
		await db.User.update({
			// 수정할 내용
			nickname: req.body.nickname
		}, {
			// 조건
			where: { id: req.user.id },
		});
		return res.send(req.body.nickname);
	}catch(e){
		console.error(e);
		next(e);
	}
})
```

```
// 포스트 삭제

router.delete('/:id', isLoggedIn, async (req, res, next) => {
	try{
		const post = await db.Post.findOne({ where: { id: req.params.id } });
		if(!post){
			return res.status(404).send('포스트가 존재하지 않습니다.');
		}
		await db.Post.destroy({ where: { id: req.params.id } });
		res.send(req.params.id);
	}catch(e){
		console.error(e);
		next(e);
	}
});
```
destroy() 메소드를 통해 레코드를 삭제할 수 있다.


## SQL에서의 관계대수 이해하기
웹에서 많은 자원을 소모하는 행위가 DB를 조회하는행위라고 한다, 관계형 DB를 사용하다보면 관계를 맺어 조회, 업데이트, 삭제 등등의 행위를
효과적으로 사용할 수 있어야한다. 그런데 글쓴이 에게는 多:多 관계에서의 through나 foreign key개념이 굉장히 헷갈렸다.

```
1:1관계
db.User.hasOne(db.Post); // 유저는 하나만갖는다, 게시글을
db.Post.belongsTo(db.User); // 게시글은 속해있다, 유저에게
위 테이블은 1:1관계인데 주가되는 table이 먼저 선언되어야한다.


1:多 관계
db.User.hasMany(db.Post); // 유저는 많이갖는다, 게시글을
db.Post.belongsTo(db.User); // 게시글은 속해있다, 유저에게
위 테이블을 1:多관계이다.


多:多 관계
db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' });
db.Hashtag.belongsToMany(db.Post, { through: 'PostHashtag' });
위 테이블은 다:다 관계이며, 多:多 관계를 표현하기위해 belongsToMany를 사용한다.
다대다 관계는 관계를 정의하며 새로운 테이블을 만들게되는데
포스트와 해쉬태그가 서로관계를 맺는걸 정의하며 정의된 새 테이블 'PostHashtag' 이 만들어지게된다. 다대다 관계는 정의할때 순서는 상관없다.

```