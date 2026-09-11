(ns contacts.app-test
  (:require [cljs.test :refer [deftest is testing use-fixtures]]
            [re-frame.core :as rf]
            [re-frame.db :as rf-db]
            [contacts.app :as app]))

;; re-frame keeps its db in a global atom (re-frame.db/app-db), and re-frame
;; subs cache derefs across dispatches. Reset both around each test so
;; events fired in one test can't leak into the next.
(use-fixtures :each
  {:before (fn [] (rf/clear-subscription-cache!) (reset! rf-db/app-db {}))})

(deftest initialize-db-sets-defaults
  (testing ":initialize-db populates the ported +page.svelte `app` object,
            field for field and value for value"
    (rf/dispatch-sync [:initialize-db])
    (is (= app/default-db @rf-db/app-db))
    (is (= "Contacts Mcp Component" @(rf/subscribe [:title])))
    (is (= "etzhayyim-project-contacts" @(rf/subscribe [:project])))
    (is (= "contacts-mcp-component" @(rf/subscribe [:name])))
    (is (= "appview" @(rf/subscribe [:kind])))
    (is (= 0 @(rf/subscribe [:route-count])))
    (is (= [] @(rf/subscribe [:routes])))
    (is (= [] @(rf/subscribe [:vars])))
    (is (true? @(rf/subscribe [:xrpc?])))
    (is (= (str "60-apps/etzhayyim-project-contacts/appview/"
                "contacts-mcp-component/svelte/src/routes/+page.svelte")
           @(rf/subscribe [:relative-path])))))

(deftest routes-sub-reflects-db-not-a-fixed-value
  (testing ":routes subscription reads whatever is in the db, not a literal"
    (reset! rf-db/app-db {:routes ["only-one.example/*"]})
    (is (= ["only-one.example/*"] @(rf/subscribe [:routes])))))

(deftest xrpc-sub-reflects-db-not-a-fixed-value
  (testing ":xrpc? subscription reads whatever is in the db, not a literal"
    (reset! rf-db/app-db {:xrpc? false})
    (is (false? @(rf/subscribe [:xrpc?])))))

(deftest initialize-db-overwrites-prior-state
  (testing ":initialize-db resets to defaults even if the db already had
            other data"
    (reset! rf-db/app-db {:title "stale" :unrelated 42})
    (rf/dispatch-sync [:initialize-db])
    (is (= app/default-db @rf-db/app-db))))

(deftest app-view-renders-hiccup-rooted-at-main-with-dads-container
  (testing "app-view returns hiccup rooted at :main, wrapping the DADS
            container, once the db is initialized"
    (rf/dispatch-sync [:initialize-db])
    (let [hiccup (app/app-view)]
      (is (vector? hiccup))
      (is (= :main (first hiccup)))
      (let [container (second hiccup)]
        (is (vector? container))
        (is (= :div (first container)))
        (is (= "dds-ext-container" (:class (second container))))))))
