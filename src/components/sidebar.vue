<template>
    <nav id="sidebarMenu" class="col-md-3 col-lg-2 d-md-block bg-light sidebar collapse">
        <div class="sidebar-sticky pt-3">

            <h6 class="sidebar-heading d-flex justify-content-between align-items-center px-3 mt-4 mb-1 text-muted">
                <span>管理員</span>
                <a class="d-flex align-items-center text-muted" href="#" aria-label="Add a new report">
                    <span data-feather="plus-circle"></span>
                </a>
            </h6>
            <ul class="nav flex-column mb-2">
                <li class="nav-item">
                    
                    <router-link class="nav-link" to="/admin/products">
                        <i class="fa-solid fa-box-open"></i>
                         產品列表
                    </router-link>

                    <router-link class="nav-link" to="">
                        <i class="fa-solid fa-list"></i>
                         訂單列表
                    </router-link>

                    <router-link class="nav-link" to="">
                        <i class="fa fa-solid fa-ticket"></i>
                         優惠券
                    </router-link>
                </li>
            </ul>

            <h6 class="sidebar-heading d-flex justify-content-between align-items-center px-3 mt-4 mb-1 text-muted">
                <span>模擬功能</span>
                <a class="d-flex align-items-center text-muted" href="#" aria-label="Add a new report">
                    <span data-feather="plus-circle"></span>
                </a>
            </h6>
            <ul class="nav flex-column mb-2">
                <li class="nav-item">
                   <router-link class="nav-link" to="/admin/customer_order">
                        <i class="fa fa-solid fa-ticket"></i>
                        模擬訂單
                    </router-link>
                </li>
            </ul>
        </div>
    </nav>
</template>

<style>

</style>