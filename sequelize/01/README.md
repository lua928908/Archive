# sequelize - mySQL ORM

## sequelize란

sequelize는 node 환경에서 사용하는 node 에서 사용하는 ORM이다.
mysql을 node 환경에서 javascript 문법으로 편리하게 사용할 수 있도록 해준다.
처음에는 sequelize가 SQL 문법을 잘 모르는 상황에서나 사용하는 것이고 순수SQL문법보다 한번 wrapping되는것이라
성능에서도 떨어진다고 알고있어서 약간은 부정적(?)으로 생각하는 부분도 있었는데 위메프에 경우 sql문이 1,000줄이 넘는경우도 있고
회사에서 참여한 sping-boot 코드랩에서 ORM을 사용하는것이 나쁜것이 아니라는 의견과 그에대한 내용을 보면서
그냥 선택의 문제인 것 같다.


```
npm i sequelize mysql2
npm i -g sequelize-cli
```
sequelize를 사용하기위해서는 mysql과 CLI가 설치되어야 한다.


```
sequelize init
```
터미널에서 sequelize init을 하면 config, models, migrations, seeders 디렉토리가 생성된다.

## 모델정의하기

#### 시퀄라이즈 기본 설정 - back/models/index.js 
 ```
const Sequelize = require('sequelize');
const env = process.env.NODE_ENV || 'development';
const config = require('../config/config')[env];
const db = {};

const sequelize = new Sequelize(config.database, config.username, config.password, config);

Object.keys(db).forEach(modelName => {
if (db[modelName].associate) {
db[modelName].associate(db);
}
});

// DB import
db.Comment = require('./commnet')(sequelize, Sequelize);
db.Hashtag = require('./hashtag')(sequelize, Sequelize);
db.Image = require('./image')(sequelize, Sequelize);
db.Post = require('./post')(sequelize, Sequelize);
db.User = require('./user')(sequelize, Sequelize);

db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db;
```

#### 유저  -  back/models/user.js
```
module.exports = (sequelize, DataTypes) => {
    const User = sequelize.define('User', { // 앞글자가 대문자면 자동으로 복수형(s가 붙음)으로 바뀐다.
        nickname: {
            type: DataTypes.STRING(20),
            allowNull: false,
        },
        userId: {
            type: DataTypes.STRING(20),
            allowNull: false,
            unique: true,
        },
        password: {
            type: DataTypes.STRING(200),
            allowNull: false,
        },
    }, {
        charset: 'utf8', // utf8로 인코딩
        collate: 'utf8_general_ci',
        timestamps: true, // 해당옵션이 true 여야 paranoid 사용 가능
        paranoid: true, // 삭제시간 기재
    });

    // db의 관계 설정
    User.associate = (db) => {
        db.User.hasMany(db.Post, { as: 'Post' });
        db.User.hasMany(db.Comment);
        db.User.belongsToMany(db.Post, { through: 'Like', as: 'Liked' });
        db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followers' });
        db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followings' });
    }

    return User;
}
```

#### 게시글  -  back/models/post.js
```
module.exports = (sequelize, DataTypes) => {
    const Post = sequelize.define('Post', {
        content: {
            type: DataTypes.TEXT, // 몇글자인지 모르는 긴 글
            allowNull: false,
        },
    }, {
        charset: 'utf8mb4', // 한글 + 이모티콘을 사용한다.
        collate: 'utf8mb4_general_ci',
    });
    Post.associate = (db) => {
        db.Post.belongsTo(db.User);
        db.Post.hasMany(db.Comment);
        db.Post.hasMany(db.Image);
        db.Post.belongsTo(db.Post, { as: 'Retweet' }); // Post는 리트윗될 수 있다, 이름이 같기때문에 as 구분한다.
        db.Post.belongsToMany(db.User, { through: 'Like' });
        db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' }); // Post + Hashtag는 多 : 多 관계
        db.Post.belongsToMany(db.User, { through: 'Like', as: 'Likers' });
    };
    return Post;
}
```

#### 댓글  -  back/models/comment.js
```
module.exports = (sequelize, DataTypes) => {
    const Comment = sequelize.define('Comment', {
        content: {
            type: DataTypes.TEXT,
            allowNull: false,
        },
    }, {
        charset: 'utf8mb4',
        collate: 'utf8mb4_general_ci',
    });
    Comment.associate = (db) => {
        db.Comment.belongsTo(db.User);
        db.Comment.belongsTo(db.Post);
    };
    return Comment;
}
```

#### 해쉬태그 - back/models/hashtag.js
```
module.exports = (sequelize, DataTypes) => {
    const Hashtag = sequelize.define('Hashtag', {
        name: {
            type: DataTypes.STRING(20),
            allowNull: false,
        },
    }, {
        charset: 'utf8mb4',
        collate: 'utf8mb4_general_ci',
    });
    Hashtag.accosiate = (db) => {
        db.Hashtag.belongsToMany(db.Post, { through: 'PostHashtag' }); // Hashtag + Post는 多 : 多 관계
    };
    return Hashtag;
}
```

```
    age: {
        DataTypes: DataTypes.INTEGER.UNSIGNED, // 음수를 받지 않는다.
    },
```

위에 코드를 사용해 음수를 받지 않을 수 있다.

[여기]("https://sequelize.org/v5/manual/data-types.html")를 누르면 공식문서의 데이터타입을 확인 할 수 있다.

```
sequelize db:create
```

db:create를 해야만 config에 기재된 db이름대로 스키마를 생선한다. 