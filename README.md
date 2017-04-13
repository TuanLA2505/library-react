var SearchPanel = React.createClass({
    onChange: function (e) {
        e.preventDefault();
        alert('onChange');
    },
    render: function () {
        return React.DOM.div(
            {
                className: 'col-sm-4'
            },
            React.DOM.form(
                null,
                React.DOM.div(
                    {
                        className: 'form-group'
                    },
                    React.DOM.label(
                        {
                            htmlFor: 'searchKey'
                        },
                        'Search'),
                    React.DOM.input(
                        {
                            id: 'searchKey',
                            className: 'form-control',
                            onChange: this.onChange,
                            placeholder: 'Search something'
                        })
                )
            )
        );
    }
});

var books = [{ id: 1, title: 'Book 1', category: 'Drama' }, { id: 2, title: 'Book 2', category: 'Fiction' }];

var BookTable = React.createClass({
    _rows: function () {
        return this.state.books.map(function (book) {
            return React.createElement(BookRow, {
                editBook: this.props.editBook,
                book: book,
                key: book.id
            });
        }, this);
    },
    getInitialState: function () {
        return {
            books: this.props.books
        };
    },
    render: function () {
        return React.DOM.div(
            {
                className: 'col-sm-4'
            },
            React.DOM.table(
                {
                    className: 'table table-striped'
                },
                React.DOM.thead(
                    null,
                    React.DOM.tr(
                        null,
                        React.DOM.th(
                            null,
                            'Title'
                        ),
                        React.DOM.th(
                            null,
                            'Category'
                        ),
                        React.DOM.th(
                            null,
                            ''
                        )
                    )
                ),
                React.DOM.tbody(
                    {},
                    this._rows()
                )
            ))
    }
});

var BookRow = React.createClass({
    editClick: function (e) {
        e.preventDefault();
        this.props.editBook(this.state.book);
    },
    getInitialState: function () {
        return {
            book: this.props.book
        };
    },
    render: function () {
        return React.DOM.tr(
            null,
            React.DOM.td(null, this.state.book.title),
            React.DOM.td(null, this.state.book.category),
            React.DOM.td(null,
                React.DOM.button(
                    {
                        className: 'btn btn-link',
                        onClick: this.editClick
                    },
                    'Edit')
            )
        );
    }
});

var BookForm = React.createClass({
    onChange: function (e) {
        e.preventDefault();

    },
    updateBook: function (e) {
        e.preventDefault();
        this.state.book.title = this.refs.title.value;
        this.state.book.category = this.refs.category.value;
        this.props.update(this.state.book);
    },
    getInitialState: function () {
        return {
            book: this.props.book
        }
    },
    componentWillReceiveProps: function (props) {
        this.setState({ book: props.book });
        this.refs.title.value = props.book.title;
        this.refs.category.value = props.book.category;
    },
    render: function () {
        return React.DOM.form(
            {
                className: 'col-sm-4'
            },
            React.DOM.div(
                {
                    className: 'form-group'
                },
                React.DOM.label(
                    {
                        htmlFor: 'title'
                    },
                    'Title'
                ),
                React.DOM.input(
                    {
                        id: 'title',
                        className: 'form-control',
                        placeholder: 'Enter book title',
                        onChange: this.onChange,
                        ref: 'title'
                    })
            ),
            React.DOM.div(
                {
                    className: 'form-group'
                },
                React.DOM.label(
                    {
                        htmlFor: 'category'
                    },
                    'Category'
                ),
                React.DOM.select(
                    {
                        ref: 'category',
                        className: 'form-control',
                        id: 'category'
                    },
                    ['Drama', 'Fiction'].map(function (option) {
                        return React.DOM.option({ value: option, key: option }, option);
                    })
                )
            ),
            React.DOM.button(
                {
                    className: 'btn btn-default',
                    onClick: this.updateBook
                },
                this.state.book.id ? 'Update' : 'Add'
            )
        );
    }
});

var Library = React.createClass({
    getInitialState: function () {
        return {
            books: books,
            currentBook: {}
        }
    },
    editBook: function (book) {
        this.setState({
            currentBook: book
        });
    },
    updateBook: function (currentBook) {
        if (currentBook.id) {
            var book = this.state.books.find(function (book) {
                if (book.id === currentBook.id) {
                    return book;
                }
            });
            book = currentBook;
            //this.state.books.replace(book, currentBook);
        }
        else {
            currentBook.id = this.state.books.length + 1;
            this.state.books.push(currentBook);
        }

        this.setState({
            currentBook: {},
            books: this.state.books
        });
    },
    render: function () {
        return React.DOM.div(
            null,
            React.createElement(SearchPanel),
            React.createElement(BookTable, { books: this.state.books, editBook: this.editBook }),
            React.createElement(BookForm, { update: this.updateBook, book: this.state.currentBook })
        );
    }
});

ReactDOM.render(
    React.createElement(Library),
    document.getElementById('library')
);
