# SimpleTodo

SimpleTodo is simple Node.js REST API for Todo interview task.

## Simple usage

Just run (while in `server` directory)

```
docker-compose up
```

The server will listen on localhost port `3000`.

## API documentation

### Get all todos

```
~|⇒  http -pb :3000/api/v1/todo/
[]
```

This method also accepts filters in the form of query parameters `description` and `done`.

### Create new todo

```
~|⇒  http -pb POST :3000/api/v1/todo/ description='First todo'
{
    "_id": "5fa2f26d7222f300103400da",
    "createdAt": "2020-11-05T07:52:21.235Z",
    "description": "First todo",
    "done": false,
    "updatedAt": "2020-11-05T07:52:21.235Z"
}
```

### Change a todo

```
~|⇒  http -pb PUT :3000/api/v1/todo/5fa2f26d7222f300103400da done:=true
{
    "_id": "5fa2f26d7222f300103400da",
    "createdAt": "2020-11-05T07:52:21.235Z",
    "description": "First todo",
    "done": true,
    "updatedAt": "2020-11-05T07:53:26.388Z"
}
```

### Delete a todo

```
~|⇒  http DELETE :3000/api/v1/todo/5fa2f26d7222f300103400da
HTTP/1.1 204 No Content
```
