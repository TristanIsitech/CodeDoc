*Source : https://merlino.agency/blog/clean-architecture-in-express-js-applications*

![[Pasted image 20221216113441.png]]

```
1 .
2 │ README.md
3 │ package.json
4 │ ...
5 │
6 └─── app
7 │ │ app.js // 5. Framework and Drivers
8 │ │ server.js // start master and forks other workers
9 │ │ config.js // get config from env or config file
10│ │ database.js
11│ │ logger.js
12│ │
13│ └─── models // 1.a) entities (i.e., Mongoose models)
14│ │ │ index.js
15│ │ │ orders.model.js
16│ │ │ ...
17│ │
18│ └─── data-access // 1.b) entities (i.e., abstraction of Mongoose models)
19│ │ │ index.js
20│ │ │ orders.db.js
21│ │ │ ...
22│ │
23│ └─── use-cases // 2) Use cases
24│ │ └─── orders
25│ │ │ │ index.js
26│ │ │ │ post-order.js
27│ │ │ │ ..
28│ │ └─── ...
29│ │
30│ └─── controllers // 3) Interface Adapters
31│ │ │ order.controller.js
32│ │ │ ...
33│ │
34│ └─── routes // 4) Routes
35│ │ │ order.routes.js
36│ │ │ ...
37│
38└─── ...
```

# Entities
``` js
// /models/index.js
import Orders from './orders.model';

// ...
export {
	Orders,
	// ...
};
```

```js
// /data-access/orders.db.js

// outside libraries are ok to import
import {v4 as uuid} from "uuid";
import _ from "lodash";

export default function makeOrdersDb({Orders}) {
	async function findOne(_filter, _options = {}) {
		const {populate, sort} = _options;
		const query = Orders.findOne(_filter);
		if (sort) query.sort(sort);
		_.forEach(populate || [], (p) => query.populate(p));
		return query.lean().exec();
	}

	async function insert({id: _id = uuid(), ...orderInfo}) {
		return Orders.create({_id, ...orderInfo});
	}
	
	async function update(_filter, _orderInfo) {
		return Orders.findOneAndUpdate(_filter, _orderInfo, {new: true});
	}
	
	async function remove(_id) {
		const res = await Orders.deleteOne({_id});
		return {
			found: res.n,
			deleted: res.deletedCount
		};
	}
	
	async function find(_filter, _options = {}) {
		const {populate} = _options;
		const query = Orders.find(_filter);
		if (populate) _.forEach(populate || [], (p) => query.populate(p));
		return query.lean().exec();
	}
	
	async function aggregate(pipeline = []) {
		return Orders.aggregate(pipeline);
	}
	
	async function paginate(_query, _options) {
		const {sort, populate, page, limit} = _options;
		return Orders.paginate(_query, {
			sort,
			lean: true,
			page,
			limit,
			populate
		});
	}
	
	return Object.freeze({
		findOne,
		insert,
		update,
		remove,
		find,
		aggregate,
		paginate
	});
}
```

```js
// /data-access/index.js

// your DB model definitions, in OneFood's case they are Mongoose files
import * as models from "../models";
import makeOrdersDb from "./orders.db";

const ordersDb = makeOrdersDb(models);

export {
	ordersDb,
	...
};
```

# Use Cases

```js
// /use-cases/orders/post-order.js

export default function makePostOrder({
	ordersDb,
	orderItemsDb,
	getPromoCodeDetails
}){
	return async function postOrder({body, headers, user}) {
	
		// Validate parameters...
		// Create order...
		// Create order items...
		
		return order;
	};
}
```

```js
// /use-cases/orders/index.js

import {
	ordersDb,
	// ...
} from 'data-access';

import Config from 'config';

import makePostOrder from './post-order';
// ...

const postOrder = makePostOrder({
	ordersDb,
	Config,
	// other injected dependencies ...
});

// Export a service containing all Use Cases ...
const orderService = Object.freeze({
	postOrder,
	// ...
});

export default orderService;

// ... and also every Use Case singularly
export {
	postOrder,
	// ...
};
```

# Interface Adapters

```js
// /controllers/order.controller.js

import {
	postOrder,
	// ...
} from 'use-cases/orders';

import {
	postPaymentLink
} from 'use-cases/payments';

export default Object.freeze({
	postOrder: (httpRequest) => postOrder(httpRequest, postPaymentLink),
	// ...
});
```

# Routes
```js
// /routes/order.routes.js

import express from 'express';

import controller from 'controllers/api/orderController';
import makeExpressCallback from 'routes/make-callback';

const router = express.Router();

router.route('/order').post(makeExpressCallback(controller.postOrder));
router.route('/order/:orderId').get(makeExpressCallback(controller.getOrderById));
// ...

export default router;
```

```js
// /routes/index.js

import express from 'express';
import token from 'policies/token';
import orders from './orders';
// ...

const router = express.Router();

// Bonus: you can split this /routes folder in 2: private and public.
// In the private index.js file you would precede all routes declaration
// with a function that checks if the authentication token is present
// in all requests and it's valid.
router.use(token);
router.use(orders);

export default router;
```

# Frameworks and Drivers
```js
// app.js

import express from 'express';
import routes from "routes";
// other imports

// initialize express server
const app = express();

app.use("/", routes);

export default app;
```

