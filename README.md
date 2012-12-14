## Presentable - Another Presenter Bundle 

**Version: 1.0**

Presentable provides a presentation layer designed for quick and easy implementation for typical Laravel applications.

You can see this bundle in use here: [https://github.com/ShawnMcCool/laravel-io](https://github.com/ShawnMcCool/laravel-io)

### History

**1.0**
- ripped off machuga's presenter bundle and added features that may or may not be good ideas.

### Installation

Install with artisan

    php artisan bundle:install presentable

or, clone the project into **bundles/presentable**.

Then, update your bundles.php to auto-start the bundle.

    return array(
        'presentable' => array( 'auto' => true ),
    );

### Examples

**Example Model**

    class Topic extends Eloquent
    {
        public static function recent($count = 5)
        {
            return static::with('author')->order_by('created_at', 'desc')->take($count)->get();
        }

        public function author()
        {
            return $this->belongs_to('User', 'user_id');
        }

        public function tags()
        {
            return $this->has_many_and_belongs_to('Tag');
        }
    }

**Example Presenter**

    class TopicPresenter extends Presentable
    {
        public $resource_name = 'topic';

        public function url()
        {
            return URL::to_action('topics@show', array($this->id, Str::slug($this->title)));
        }

        public function link($attributes = array())
        {
            return HTML::link_to_action('topics@show', $this->title, array($this->id, Str::slug($this->title)), $attributes);
        }

        public function published_date()
        {
            return date('d-m-y', strtotime($this->created_at));
        }
    }

**Example Controller**

    class Topics_Controller extends Base_Controller
    {
        public function get_index()
        {
            $recent_topics = TopicPresenter::recent(100);
            
            $this->layout->content = View::make('topics.index')->with('recent_topics', $recent_topics);
        }
    }

**Example View**

    <h2>{{ $topic->link }}</h2>

    Published: {{ $topic->published_date }}

    <a href="{{ $topic->url }}">Link to the topic</a>